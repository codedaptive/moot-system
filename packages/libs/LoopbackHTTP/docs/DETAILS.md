---
doc: DETAILS
package: LoopbackHTTP
repo: moot-system
authored_commit: 3c3ce06528a1d1b3b6e9aa8a6008cba20a243c23
authored_date: 2026-07-07
sources:
  - path: Sources/LoopbackHTTP/HTTPWire.swift
    blob: c07525c2583c06aa8406287eef5cb17279b9b86a
  - path: Sources/LoopbackHTTP/POSIXSocket.swift
    blob: 29a9202773e71b964acc5a3ebca742c36e7cbd9f
---

# LoopbackHTTP Details

## Current Release Details

`POSIXSocket.acceptOne` marks Darwin client sockets with `SO_NOSIGPIPE`.
`POSIXSocket.sendAll` uses `MSG_NOSIGNAL` on Linux.
A peer that closes mid-response now returns a failed write.
The transport caller can handle that error path without a process signal.

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline
order. The socket transport comes first, then the HTTP protocol layer
built on top of it.

## POSIXSocket.swift

This file provides `POSIXSocket`, a set of small blocking-socket helpers.
It also provides `SocketError`, the error type they throw. Everything in
this file is synchronous. A call blocks the calling thread until the
operating system answers. Callers should run these calls off a dedicated
thread or a detached task. They should never run on a thread that must stay
responsive.

Two design choices explain the whole file. First, the code targets plain
POSIX sockets rather than Apple's `Network.framework`. That higher-level
framework refused to bind a listening server socket in this project's
command-line build environment. It returned a low-level POSIX error code on
every configuration tried. The restriction is tied to running outside an
app bundle. Plain sockets bind correctly in that same environment. The
library uses them directly, and the same security guarantees apply either
way.

Second, the file imports `Glibc` on Linux and `Darwin` on Apple platforms
through a compile-time check. The same source file compiles on both without
any conditional logic beyond the import line.

`listenLoopbackTCP(port:)` opens a TCP listening socket. It binds the
socket to `127.0.0.1`, never to `0.0.0.0`. Binding to `0.0.0.0` would accept
connections from any network interface, not just the local machine. The
bind address is a literal numeric value. It is not a name the operating
system could resolve differently, so this guarantee cannot be weakened by a
misconfigured host file or DNS entry.

Passing port zero lets the operating system pick an unused port. The
function reads the actual bound port back with `getsockname` and returns
it. This matters because the caller often needs to tell another process
which port to connect to. This function is the one place in the library
that decides which network interface can reach the server at all. It
decides in favor of the strictest possible answer every time, regardless of
what the caller asks for.

`listenUnix(path:)` opens a Unix-domain socket at a filesystem path instead
of a network port. A Unix-domain socket is a connection endpoint that lives
in the filesystem. Only other processes on the same machine can reach it. A
web browser has no way to speak to one at all. This makes it a natural
channel for the most sensitive operations, since browser-based attacks
cannot structurally reach it.

The function removes any stale socket file left over from a previous
crashed run before binding. A dead file cannot then block a fresh start.
After binding, it changes the file's permissions to owner-read-write-only
before calling `listen`. That mode is 0600. There is no window during which the
socket file exists but is readable by other users on the machine. This chmod
step is what turns "only this user can connect" from a hope into a
guarantee the filesystem itself enforces.

`acceptOne(_:)` accepts one pending connection on a listening socket. It
returns the new connection's descriptor, or `nil` if the accept call
failed. It is a thin wrapper. The decision of whether and how to retry
after a failure belongs to the caller, not to this function.

`recv(_:max:)` reads up to `max` bytes from a connected socket. It returns
them as `Data`, or `nil` on a read error. An empty but non-nil result means
the peer closed the connection cleanly. A caller reading in a loop uses that
signal to know when to stop.

`sendAll(_:_:)` writes every byte of `data` to a socket. It loops until the
whole buffer is sent or a write fails. A single call to the underlying
system write is not guaranteed to send the whole buffer at once. The kernel
is free to accept less. This function is therefore the one place other
files in the package go to guarantee that a full response, or a full SSE
frame, actually reaches the wire. It returns `false` on any failed write. A
caller uses that as its signal that the peer is gone and the connection
should be torn down.

`SocketError` is the file's structured error type. The package's error
handling rule prefers enumerated error cases over an optional value with no
explanation. A caller, or a log line, can then say exactly which system
call failed. It can name the error number too, rather than only report that
something failed. `.syscall(String, Int32)` names the failing call and carries its
`errno` value. `.pathTooLong` covers the one Unix-domain-socket case where
the caller's path does not fit the fixed-size buffer the operating system's
socket address structure provides.

## HTTPWire.swift

This file provides three public types. `HTTPRequest` is a parsed incoming
request. `HTTPResponse` is an outgoing response the caller builds.
`SSEStream` is the narrower Server-Sent-Events framing used for a
long-lived push connection. All three depend on `POSIXSocket` for the
actual byte transfer. Nothing in this file opens a socket itself.

The file's scope is deliberately narrow. It is not a general-purpose HTTP
server. It parses a request line, a set of headers, and an optional body
bounded by a `Content-Length` header. It writes exactly one response per
connection before that connection closes. It does not support chunked
transfer encoding, HTTP/1.1 keep-alive across many requests, or HTTP/2.
Every response this library writes closes the connection afterward. An SSE
stream is the one exception, since the caller keeps it open on purpose.

### HTTPRequest

`HTTPRequest` is a plain value. It holds the request `method`, for example
`"GET"`. It holds the `path`, with any query string already removed. It
also holds the raw `query` string that followed the `?`. The `query` string
is empty if there was none. It also holds a lowercase-keyed table of `headers` and the
request `body` as `Data`. Storing the path and query separately means every
consumer performs that split identically. No consumer has to write its own,
possibly buggy, parsing.

`bearerToken` reads the `Authorization` header. It checks that the header
begins with the case-insensitive prefix `"bearer "`. It returns the
remaining token text with surrounding whitespace trimmed, or `nil` if the
header is absent or does not match. This is a convenience reader only. The
library extracts the token text so a caller does not have to. It never
decides whether that token is valid. The decision to accept or reject a
request based on this value belongs entirely to the code above this
library. This keeps with the package's edition-neutral, authentication-free
design.

`origin` reads the `Origin` header directly, with no further processing. A
browser sets this header to say which web page's script initiated the
request. Like `bearerToken`, this reader exists purely so a caller can
inspect the value. The library does not compare it against an allow-list
itself.

`wantsEventStream` decides whether the caller likely wants an SSE response
instead of an ordinary one. It returns `true` if the request's `Accept`
header mentions `text/event-stream`. It also returns `true` if the query
string carries an exact `stream=1` parameter. The exact-parameter check
matters for a specific reason. An earlier version of this logic used a
substring check, `query.contains("stream=1")`. That check incorrectly
matched `stream=10`, `mystream=1`, and `x=stream=1`. None of those mean what
`stream=1` means.

The current implementation splits the query on `&`. It compares each
resulting parameter for exact equality to `"stream=1"`. A longer number or
an unrelated parameter name containing the same substring cannot fool it.
A dedicated regression test file,
`Tests/LoopbackHTTPTests/WantsEventStreamTests.swift`, exists specifically
to keep this fix from silently regressing.

This same property carries a documented security note. An SSE request made
this way is an ordinary browser `GET` request. The browser's cross-origin
preflight check never runs for it. A page on an unrelated origin can
therefore open an SSE connection to a loopback server, if that server is
reachable at all. Any consumer that serves sensitive data over SSE must
check the `origin` value itself. It must refuse an unexpected origin before
upgrading to a stream. This library does not perform that check, again by
the authentication-free design.

`HTTPRequest.read(fd:maxHeaderBytes:maxBodyBytes:)` is the entry point that
turns raw socket bytes into a parsed request. It reads repeatedly from the
socket, accumulating bytes into a buffer, until it finds the header
terminator. The terminator is `"\r\n\r\n"`, the blank line that ends an HTTP
header block. If the accumulated header block ever exceeds
`maxHeaderBytes` before that terminator appears, the function gives up and
returns `nil`. It will not keep buffering an unbounded amount of untrusted
network data. This cap protects the server's memory against a hostile or
broken client sending an endless header.

The caps are parameters rather than fixed constants, because different
listeners have different legitimate needs. A small dashboard control
listener wants a small cap. A listener serving `tools/call` requests for the
Model Context Protocol wants a larger one. Each caller can choose the cap
that matches its own traffic, without every listener sharing one compromise
value.

The private `parse(buffer:headerEnd:fd:maxBodyBytes:)` helper does the
actual parsing once the header terminator has been found. It reads the
request line and splits it into method and target. It splits the target on
the first `?` into path and query. It then walks the remaining header
lines. Each line is split on its first colon. The function trims whitespace
from each side and lowercases the header name.

This normalization matters. Later lookups, such as
`headers["content-length"]`, do not have to guess whether a client sent
`Content-Length` or `content-length` or some other capitalization. HTTP
header names are case-insensitive on the wire. This normalization is what
lets the rest of the library treat them as plain lowercase string keys.

If a `Content-Length` header is present, the function reads exactly that
many more bytes from the socket, up to `maxBodyBytes`. If the declared
length exceeds the cap, the body is silently truncated to the cap rather
than the request being rejected. A caller for whom truncation would be
dangerous should set its cap high enough that truncation cannot happen. It
can also check the `Content-Length` header itself before calling `read` at
all.

### HTTPResponse

`HTTPResponse` is a plain value: a `status` code, a table of `headers`, and
a `body`. The library builds `HTTPRequest` for the caller. This type works
the other way around. The caller builds it, and the library only
serializes it. This inversion is deliberate. An earlier version of this
code hard-coded a closed set of response shapes for one specific consumer.
Generalizing it to a plain value that any caller can construct is what let
a second consumer, the resident MCP transport, reuse the same library
without waiting for new cases to be added here every time its needs
changed.

Three static convenience constructors cover the shapes most callers need,
without limiting them to only those shapes. `json(status:body:)` builds a
response with an `application/json` content type. `asset(contentType:body:)`
builds a `200 OK` response carrying `Cache-Control: no-store`. That header
tells the browser never to reuse a cached copy. This matters for a locally
served dashboard. A stale cached page after a new build would show outdated
controls, or worse, controls that no longer match the running server's API.
`notFound` is a ready-made `404` response with a small consistent JSON error
body. Every listener reports a missing route the same way.

`send(fd:)` serializes the response and writes it to the socket through
`POSIXSocket.sendAll`. It computes `Content-Length` itself from the actual
body byte count. It overrides anything the caller supplied for that header.
A caller-supplied length that does not match the real body would produce a
response the receiving HTTP client cannot parse correctly.

Headers are written in a fixed, deterministic order. `Content-Type` and
`Content-Length` come first, if present. Every other header follows,
sorted alphabetically. A `Connection: close` line is always appended last,
regardless of anything the caller set for that header. The HTTP
specification allows headers in any order, so fixing the order this way is
not required by the specification. It makes every response byte-for-byte
reproducible for testing and logging. That reproducibility matters more
here than flexibility no caller needs.

`reason(_:)` maps a status code to its human-readable reason phrase, for
example `404` to `"Not Found"`, for the small fixed set of statuses this
library emits. It falls back to `"OK"` for anything else. The reason phrase
is advisory text. HTTP clients are not required to interpret it.

### SSEStream

`SSEStream` frames the Server-Sent-Events wire format for one open
connection, identified by its socket descriptor `fd`. `HTTPRequest` and
`HTTPResponse` each cover one request-response exchange. An `SSEStream`
value works differently. The caller holds it for the life of a
long-running push connection. The library plays no role in deciding how
long that is, what triggers each message, or when to close it.

`writeHead()` writes the fixed SSE response head. That head is the status
line and three headers a client needs to recognize an event stream:
`Content-Type: text/event-stream`, `Cache-Control: no-cache`, and
`Connection: keep-alive`. It must be called exactly once, before any data
frame. It returns `false` if the write failed. The documentation notes that
a failed write means the peer is already gone. A caller seeing `false`
should close the connection rather than attempt to send frames into a
stream nobody is reading.

`send(_:)` writes one SSE `data:` frame carrying the given payload string.
The wire format is `"data: <payload>\n\n"`, the payload text followed by
exactly one blank line, as the SSE specification requires. It also returns
`false` on a failed write, for the same reason.

Both callers of this type in MOOTx01 decide their own cadence and lifetime
entirely outside this file. The moot-mgr dashboard drives the stream from
polling its own store. The resident MCP transport drives it from JSON-RPC
notifications. `SSEStream` only guarantees that whatever they send is
framed correctly on the wire.
