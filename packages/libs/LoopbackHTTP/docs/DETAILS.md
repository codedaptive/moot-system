---
doc: DETAILS
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

# LoopbackHTTP Details

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline order:
the socket transport first, then the HTTP protocol layer built on top of it.

## POSIXSocket.swift

This file provides `POSIXSocket`, a set of small blocking-socket helpers, and
`SocketError`, the error type they throw. Everything in this file is
synchronous: a call blocks the calling thread until the operating system
answers. Callers are expected to run these calls off a dedicated thread or a
detached task, never on a thread that must stay responsive.

Two design choices explain the whole file. First, the code targets plain
POSIX sockets rather than Apple's `Network.framework`, because that
higher-level framework refused to bind a listening server socket in this
project's command-line build environment; it returned a low-level POSIX error
code on every configuration tried, a restriction tied to running outside an
app bundle. Plain sockets bind correctly in that same environment, so the
library uses them directly, and the same security guarantees apply either
way. Second, the file imports `Glibc` on Linux and `Darwin` on Apple
platforms through a compile-time check, so the same source file compiles on
both without any conditional logic beyond the import line.

`listenLoopbackTCP(port:)` opens a TCP listening socket and binds it to
`127.0.0.1`, never to `0.0.0.0` (which would accept connections from any
network interface, not just the local machine). The bind address is a
literal numeric value, not a name the operating system could resolve
differently, so this guarantee cannot be weakened by a misconfigured host
file or DNS entry. Passing port `0` lets the operating system pick an unused
port; the function reads the actual bound port back with `getsockname` and
returns it, which matters because the caller often needs to tell another
process which port to connect to. Why this function matters: it is the one
place in the library that decides which network interface can reach the
server at all, and it decides in favor of the strictest possible answer,
every time, regardless of what the caller asks for.

`listenUnix(path:)` opens a Unix-domain socket at a filesystem path instead of
a network port. A Unix-domain socket is a connection endpoint that lives in
the filesystem and can only be reached by other processes on the same
machine; a web browser has no way to speak to one at all. This makes it a
natural channel for the most sensitive operations, since browser-based
attacks are structurally unable to reach it. The function removes any stale
socket file left over from a previous crashed run before binding, so a dead
file cannot block a fresh start. After binding, it changes the file's
permissions to owner-read-write-only (mode 0600) before calling `listen`, so
there is no window during which the socket file exists but is readable by
other users on the machine. Why this matters: the chmod step closing that
window is what turns "only this user can connect" from a hope into a
guarantee enforced by the filesystem itself.

`acceptOne(_:)` accepts one pending connection on a listening socket and
returns the new connection's descriptor, or `nil` if the accept call failed.
It is a thin wrapper; the decision of whether and how to retry after a
failure belongs to the caller, not to this function.

`recv(_:max:)` reads up to `max` bytes from a connected socket and returns
them as `Data`, or `nil` on a read error. An empty (but non-nil) result means
the peer closed the connection cleanly; a caller reading in a loop uses that
to know when to stop.

`sendAll(_:_:)` writes every byte of `data` to a socket, looping until the
whole buffer is sent or a write fails. A single call to the underlying system
write is not guaranteed to send the whole buffer at once — the kernel is free
to accept less — so this function is the one place other files in the
package go to guarantee that a full response, or a full SSE frame, actually
reaches the wire. It returns `false` on any failed write, which a caller uses
as its signal that the peer is gone and the connection should be torn down.

`SocketError` is the file's structured error type. The package's error
handling rule prefers enumerated error cases over returning an optional
value with no explanation, so that a caller (or a log line) can say exactly
which system call failed and with which error number, rather than only that
something failed. `.syscall(String, Int32)` names the failing call and
carries its `errno` value; `.pathTooLong` covers the one Unix-domain-socket
case where the caller's path does not fit the fixed-size buffer the
operating system's socket address structure provides.

## HTTPWire.swift

This file provides three public types: `HTTPRequest`, a parsed incoming
request; `HTTPResponse`, an outgoing response the caller builds; and
`SSEStream`, the narrower Server-Sent-Events framing used for a long-lived
push connection. All three depend on `POSIXSocket` for the actual byte
transfer; nothing in this file opens a socket itself.

The file's scope is deliberately narrow. It is not a general-purpose HTTP
server: it parses a request line, a set of headers, and an optional body
bounded by a `Content-Length` header, and it writes exactly one response per
connection before that connection closes. It does not support chunked
transfer encoding, HTTP/1.1 keep-alive across many requests on the same
response path, or HTTP/2. Every response this library writes closes the
connection afterward (except an SSE stream, which the caller keeps open on
purpose).

### HTTPRequest

`HTTPRequest` is a plain value holding the request `method` (for example,
`"GET"`), the `path` with any query string already removed, the raw `query`
string that followed the `?` (or an empty string if there was none), a
lowercase-keyed table of `headers`, and the request `body` as `Data`. Storing
the path and query separately, rather than leaving the caller to split a
combined URL string, means every consumer performs that split identically
instead of each one writing its own (and possibly buggy) parsing.

`bearerToken` reads the `Authorization` header, checks that it begins with
the case-insensitive prefix `"bearer "`, and returns the remaining token text
with surrounding whitespace trimmed, or `nil` if the header is absent or does
not match. Why this matters: this is a convenience reader only. The library
extracts the token text so a caller does not have to; it never decides
whether that token is valid. The decision to accept or reject a request
based on this value belongs entirely to the code above this library, in
keeping with the package's edition-neutral, authentication-free design.

`origin` reads the `Origin` header directly, with no further processing. A
browser sets this header to say which web page's script initiated the
request. Like `bearerToken`, this is exposed purely so a caller can inspect
it; the library does not compare it against an allow-list itself.

`wantsEventStream` decides whether the caller likely wants an SSE response
instead of an ordinary one. It returns `true` if the request's `Accept`
header mentions `text/event-stream`, or if the query string carries an exact
`stream=1` parameter. The exact-parameter check matters because an earlier
version of this logic used a substring check, `query.contains("stream=1")`,
which incorrectly matched `stream=10`, `mystream=1`, and `x=stream=1` — none
of which mean what `stream=1` means. The current implementation splits the
query on `&` and compares each resulting parameter for exact equality to
`"stream=1"`, which cannot be fooled by a longer number or an unrelated
parameter name that happens to contain the same substring. A dedicated
regression test file, `Tests/LoopbackHTTPTests/WantsEventStreamTests.swift`,
exists specifically to keep this fix from silently regressing. This same
property carries a documented security note: because an SSE request made
this way is an ordinary browser `GET` request, the browser's cross-origin
preflight check never runs for it. A page on an unrelated origin can
therefore open an SSE connection to a loopback server if that server is
reachable at all. Any consumer that serves sensitive data over SSE must check
the `origin` value itself and refuse an unexpected origin before upgrading to
a stream; this library does not perform that check, again by the
authentication-free design.

`HTTPRequest.read(fd:maxHeaderBytes:maxBodyBytes:)` is the entry point that
turns raw socket bytes into a parsed request. It reads repeatedly from the
socket, accumulating bytes into a buffer, until it finds the header
terminator (`"\r\n\r\n"`, the blank line that ends an HTTP header block). If
the accumulated header block ever exceeds `maxHeaderBytes` before that
terminator appears, the function gives up and returns `nil` rather than
continuing to buffer an unbounded amount of untrusted network data — a cap
that protects the server's memory against a hostile or broken client sending
an endless header. The caps are parameters rather than fixed constants
because different listeners have different legitimate needs: a small
dashboard control listener wants a small cap, while a listener serving
`tools/call` requests for the Model Context Protocol wants a larger one, so
each caller can choose the cap that matches its own traffic without every
listener sharing one compromise value.

The private `parse(buffer:headerEnd:fd:maxBodyBytes:)` helper does the actual
parsing once the header terminator has been found. It reads the request
line, splits it into method and target, and splits the target on the first
`?` into path and query. It then walks the remaining header lines, splitting
each on its first colon, trimming whitespace, and lowercasing the header
name so that later lookups (`headers["content-length"]`, for example) do not
have to guess whether a client sent `Content-Length` or `content-length` or
some other capitalization; HTTP header names are case-insensitive, and this
normalization is what makes the rest of the library able to treat them as
plain lowercase string keys. If a `Content-Length` header is present, the
function reads exactly that many more bytes from the socket, up to
`maxBodyBytes`. If the declared length exceeds the cap, the body is silently
truncated to the cap rather than the request being rejected; a caller for
whom truncation would be dangerous is expected to set its cap high enough
that truncation cannot happen, or to check the `Content-Length` header itself
before calling `read` at all.

### HTTPResponse

`HTTPResponse` is a plain value: a `status` code, a table of `headers`, and a
`body`. Unlike `HTTPRequest`, which the library builds for the caller, this
type is built by the caller and only serialized by the library. This
inversion is deliberate: the earlier version of this code hard-coded a
closed set of response shapes for one specific consumer, and generalizing it
to a plain value that any caller can construct is what let a second consumer
(the resident MCP transport) reuse the same library without waiting for new
cases to be added here every time its needs changed.

Three static convenience constructors cover the shapes most callers need
without limiting them to only those shapes. `json(status:body:)` builds a
response with an `application/json` content type. `asset(contentType:body:)`
builds a `200 OK` response carrying `Cache-Control: no-store`, a header that
tells the browser never to reuse a cached copy; this matters for a locally
served dashboard, where a stale cached page after a new build would show
outdated controls or, worse, controls that no longer match the running
server's API. `notFound` is a ready-made `404` response with a small
consistent JSON error body, so every listener reports a missing route the
same way.

`send(fd:)` serializes the response and writes it to the socket through
`POSIXSocket.sendAll`. It computes `Content-Length` itself from the actual
body byte count and overrides anything the caller supplied for that header,
because a caller-supplied length that does not match the real body would
produce a response the receiving HTTP client cannot parse correctly. Headers
are written in a fixed, deterministic order: `Content-Type` and
`Content-Length` first if present, then every other header sorted
alphabetically, and finally a `Connection: close` line that is always
appended last regardless of anything the caller set for that header. Fixing
the order this way is not required by the HTTP specification, which allows
headers in any order, but it makes every response byte-for-byte reproducible
for testing and logging, which matters more here than flexibility that no
caller needs. `reason(_:)` maps a status code to its human-readable reason
phrase (for example, `404` to `"Not Found"`) for the small fixed set of
statuses this library emits, falling back to `"OK"` for anything else since
the reason phrase is advisory text that HTTP clients are not required to
interpret.

### SSEStream

`SSEStream` frames the Server-Sent-Events wire format for one open
connection, identified by its socket descriptor `fd`. Unlike `HTTPRequest`
and `HTTPResponse`, which each cover one request-response exchange, an
`SSEStream` value is meant to be held by the caller for the life of a
long-running push connection; the library plays no role in deciding how long
that is, what triggers each message, or when to close it.

`writeHead()` writes the fixed SSE response head — the status line and the
three headers (`Content-Type: text/event-stream`, `Cache-Control: no-cache`,
`Connection: keep-alive`) a client needs to recognize an event stream — and
must be called exactly once, before any data frame. It returns `false` if the
write failed, which the documentation notes means the peer is already gone;
a caller seeing `false` should close the connection rather than attempt to
send frames into a stream nobody is reading. `send(_:)` writes one SSE
`data:` frame carrying the given payload string, in the wire format
`"data: <payload>\n\n"` that the SSE specification requires (the payload
text followed by exactly one blank line). It also returns `false` on a failed
write, for the same reason. Both callers of this type in MOOTx01 — moot-mgr's
dashboard, which drives the stream from polling its own store, and the
resident MCP transport, which drives it from JSON-RPC notifications — decide
their own cadence and lifetime entirely outside this file; `SSEStream` only
guarantees that whatever they send is framed correctly on the wire.
