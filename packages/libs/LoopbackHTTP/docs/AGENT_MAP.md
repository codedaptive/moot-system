---
doc: AGENT_MAP
package: LoopbackHTTP
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
sources:
  - path: Sources/LoopbackHTTP/HTTPWire.swift
    blob: c07525c2583c06aa8406287eef5cb17279b9b86a
  - path: Sources/LoopbackHTTP/POSIXSocket.swift
    blob: 6dfba4dee7ad3121d47c8bfc937854c6f13ad1b1
---

# AGENT_MAP — LoopbackHTTP

PURPOSE: zero-dependency, loopback-pinned HTTP/1.1 server primitive (transport + wire parsing/framing only; no auth/policy). Bind loopback socket → accept → recv bytes → HTTPRequest.read parses → caller builds HTTPResponse or drives SSEStream → send/sendAll writes bytes back.

DEPS: imports Foundation (HTTPWire.swift); Glibc (Linux) / Darwin (Apple) via `#if canImport(Glibc)` guard (POSIXSocket.swift). No external SwiftPM packages (zero-dependency kit rule). No rust/ port — extraction target is OS transport glue, exempt from the Swift/Rust parity discipline (ADR-LOOPBACKHTTP-001); parity for the MCP transport is enforced at the JSON-RPC wire, not here. Imported by: moot-mgr (monitor daemon dashboard read-API + control listener), resident mootx01 MCP daemon (JSON-RPC transport, SSE notifications).

ENTRY POINTS (most callers need only these):
- POSIXSocket.swift:43 `POSIXSocket.listenLoopbackTCP(port:) throws -> (fd, port)` — bind+listen, loopback-only
- HTTPWire.swift:95 `HTTPRequest.read(fd:maxHeaderBytes:maxBodyBytes:) -> HTTPRequest?` — parse one request off a connected fd
- HTTPWire.swift:201 `HTTPResponse.send(fd:)` — serialize + write one response
- HTTPWire.swift:252/:259 `SSEStream.writeHead()` / `.send(_:)` — SSE head + data frames

## Symbol Table

### Transport — POSIXSocket.swift
- :32 `enum POSIXSocket` — namespace; all calls synchronous/blocking, run off cooperative pool
- :43 `listenLoopbackTCP(port:) throws -> (fd: Int32, port: UInt16)` — binds hard-pinned to 127.0.0.1 (never INADDR_ANY); port 0 = OS-assigned, read back via getsockname; listen backlog 16
- :91 `listenUnix(path:) throws -> Int32` — UDS listener; unlinks stale path first; chmod 0600 BEFORE listen (closes bindable-but-world-readable window); listen backlog 16
- :134 `acceptOne(_ listenFD:) -> Int32?` — accept one conn; nil on failure, caller decides retry
- :141 `recv(_:max:) -> Data?` — up to `max` bytes; empty non-nil Data = clean EOF; nil = read error
- :150 `sendAll(_:_:) -> Bool` — loops write() until all bytes sent or a write fails; single source of truth for "fully flushed to socket" used by both HTTPResponse.send and SSEStream
- :166 `enum SocketError: Error, Sendable, Equatable` — structured errors (project rule: enums not bare optionals)
- :168 `case syscall(String, Int32)` — named syscall + errno
- :170 `case pathTooLong` — UDS path exceeds fixed sun_path buffer

### Request — HTTPWire.swift
- :27 `struct HTTPRequest: Sendable` — method/path(no query)/query(raw, no '?')/headers(lowercased keys)/body
- :36 `init(method:path:query:headers:body:)` — public memberwise; used directly by tests to synthesize requests
- :46 `bearerToken: String?` — reads Authorization, case-insensitive "bearer " prefix strip + trim; CONVENIENCE ONLY, no validation
- :54 `origin: String?` — raw Origin header passthrough; CONVENIENCE ONLY, no allow-list check
- :69 `wantsEventStream: Bool` — Accept:text/event-stream OR exact query param `stream=1` (split on '&', exact match — NOT substring; see GOTCHAS)
- :95 `static func read(fd:maxHeaderBytes:maxBodyBytes:) -> HTTPRequest?` — buffers until "\r\n\r\n"; nil if header block exceeds maxHeaderBytes before terminator found, or on recv failure
- :114 `private static func parse(buffer:headerEnd:fd:maxBodyBytes:) -> HTTPRequest?` — request-line split, header lowercase-key parse, Content-Length-bounded body read (truncates silently at maxBodyBytes, does not error)

### Response — HTTPWire.swift
- :164 `struct HTTPResponse: Sendable` — status/headers/body, caller-composed (not a closed consumer-specific case set — see GOTCHAS)
- :169 `init(status:headers:body:)` — headers/body default to empty
- :176 `static func json(status:body:) -> HTTPResponse` — Content-Type: application/json
- :182 `static func asset(contentType:body:) -> HTTPResponse` — 200 + Cache-Control: no-store (redeployed UI never served stale)
- :191 `static var notFound: HTTPResponse` — 404 + `{"error":"not_found"}`
- :201 `func send(fd:)` — computes Content-Length from actual body (overrides any caller value); header order: Content-Type, Content-Length, then rest sorted alphabetically, then Connection: close ALWAYS last (caller-supplied Connection does not suppress it)
- :221 `static func reason(_ status:) -> String` — reason phrase for 200/400/401/403/404/413/500; default "OK"

### SSE — HTTPWire.swift
- :242 `struct SSEStream: Sendable` — long-lived push connection wrapper; caller owns source/cadence/lifetime/fd close
- :245 `init(fd:)`
- :252 `func writeHead() -> Bool` — call exactly once before any frame; false = peer already gone
- :259 `func send(_ payload:) -> Bool` — writes `"data: \(payload)\n\n"`; false = write failed, caller should stop
- :264 `static let responseHead: Data` — fixed head: 200 OK, text/event-stream, no-cache, keep-alive

## INVARIANTS / GOTCHAS

- AUTH-FREE INVARIANT (ADR-LOOPBACKHTTP-001 condition 3): no auth/policy logic anywhere in this package. `bearerToken`/`origin` are read-only conveniences; accept/reject is composed ABOVE this library by each consumer (none in Community Edition, bearer+Origin in moot-mgr, OAuth 2.1 in EE-only remote layer). Do not add auth enforcement here — it would break the "one binary, all editions" property.
- LOOPBACK-ONLY BY CONSTRUCTION: `listenLoopbackTCP` binds the literal `127.0.0.1` (0x7F000001 big-endian), never `INADDR_ANY`/0.0.0.0. Do not parameterize the bind address to accept a caller-supplied host.
- UDS PERMISSION WINDOW: `listenUnix` must chmod 0600 BEFORE `listen()`, not after — the current ordering closes the world-readable window; do not reorder.
- SSE CSRF NOTE: `wantsEventStream` via `?stream=1` is a plain GET, so browser CORS preflight does NOT run. A cross-origin page can open an SSE connection if the loopback port is reachable. Any consumer serving sensitive data over SSE MUST check `request.origin` itself before upgrading — this library does not and will not enforce it (see AUTH-FREE INVARIANT).
- `wantsEventStream` query check is EXACT-MATCH on a `&`-split parameter, not `query.contains("stream=1")`. The substring form was a real bug (matched "stream=10", "mystream=1", "x=stream=1"); regression-locked by Tests/LoopbackHTTPTests/WantsEventStreamTests.swift. Do not revert to substring matching.
- Header names are lowercased on parse (`parse(buffer:...)`) so all internal/consumer lookups (`headers["content-length"]`, `headers["authorization"]`) use lowercase keys only — HTTP header names are case-insensitive on the wire but this dictionary is not.
- Body truncation, not rejection: `HTTPRequest.read`/`parse` silently truncate a body at `maxBodyBytes` when Content-Length exceeds it; they do not return nil or an error. A listener that must reject oversize bodies (e.g. the MCP `tools/call` listener) needs to check Content-Length itself before/after calling, or size its cap so truncation cannot corrupt a valid request.
- `maxHeaderBytes`/`maxBodyBytes` are per-call parameters, not fixed constants (default 64 KiB each) — deliberately, so a small dashboard control listener and a larger MCP listener each pick their own cap (ADR-LOOPBACKHTTP-001 condition 2).
- `HTTPResponse.send` always appends `Connection: close` last, even if the caller supplied their own `Connection` header (that value is emitted earlier, in the sorted section, and does not suppress the trailing one). Every response this library writes closes the connection; there is no keep-alive path for ordinary responses (SSE is the sole long-lived exception).
- `HTTPResponse`/`SSEStream` are open, caller-composed value types, not a closed enum of consumer-specific response shapes — this genericity (added in P1a) is what let a second consumer (resident MCP daemon) reuse the library without modifying it. Do not narrow them back to a fixed case set for one consumer's convenience.
- All `POSIXSocket` calls are synchronous/blocking. Callers must run them off a dedicated Thread or `Task.detached`, never inline on a thread that must stay responsive.
- Network.framework (`NWListener`) was tried and rejected: it returns POSIXErrorCode 22/EINVAL binding a listening socket in this project's command-line (non-app-bundle) build environment, on every configuration tested. Do not reintroduce it as "simpler" without re-verifying that constraint is gone.
- Swift-only, no Rust port: this library is OS-transport glue, not deterministic substrate compute, so it is exempt from the Swift/Rust byte-parity discipline that governs libraries like LatticeLib. ARIA_MCP-rust hand-rolls its own std::net transport under a no-FFI rule; the two sides agree only at the JSON-RPC message level.
- Zero external SwiftPM dependencies (Package.swift target has none) — adding one requires re-justifying against the kit dependency rules that motivated this extraction in the first place.
