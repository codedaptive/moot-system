---
doc: OVERVIEW
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

# LoopbackHTTP Overview

## What This Library Does

LoopbackHTTP is a small HTTP/1.1 server primitive. It listens on a socket. It
reads one request. It lets the caller write one response.

The library also frames a Server-Sent-Events (SSE) stream. SSE is a way for a
server to push a series of small text messages to a browser. The messages
travel over one open connection. The library does not decide what the
traffic means. It only moves the bytes correctly.

The library binds strictly to the loopback address, `127.0.0.1`. A loopback
address is one that only the same machine can reach. No other computer on the
network can connect to it. Every listener this library creates is reachable
only from processes on the same device.

## The Problem It Solves

Two programs inside MOOTx01 need a local HTTP server. The moot-mgr monitor
daemon serves a small dashboard. The resident mootx01 MCP daemon serves the
Model Context Protocol used by AI tool integrations.

Before this library existed, each program hand-rolled its own socket and
parsing code. Two hand-rolled copies drift apart over time. One gets a
security fix and the other does not. One handles a header correctly and the
other does not.

LoopbackHTTP was extracted from moot-mgr under decision record
ADR-LOOPBACKHTTP-001. Both programs now share one audited implementation
instead of maintaining two.

The library adds no external package dependency. It wraps only the system C
socket API supplied by the operating system: `libc` on Linux and `Darwin` on
Apple platforms. A larger, general-purpose HTTP package such as SwiftNIO was
available. It was rejected because it would pull in a dependency graph
broader than this narrow need justifies. The moot-system kit rules ask each
library to justify every dependency it takes.

Even Apple's own `Network.framework` was tried. It was rejected for a
concrete reason. Its listener type refused to bind a listening server socket
in this project's command-line build environment. It failed with a
low-level error code on every configuration tested. Plain POSIX sockets bind
correctly in that same environment. The library builds on them directly.

The library is deliberately authentication-free. It exposes two convenience
readers on a parsed request, `bearerToken` and `origin`, so a caller can read
those header values easily. The library itself never decides whether a
request is authorized. That decision sits one layer above the transport, in
each consumer. The Community Edition of moot-mgr enforces no policy at all.
The full moot-mgr enforces a bearer token plus an Origin header check. An
Enterprise Edition-only remote layer enforces OAuth 2.1. Keeping the
transport policy-free lets the identical compiled library ship inside every
edition unchanged. Only the layer above it differs.

The library is Swift-only. MOOTx01 keeps a byte-for-byte parity discipline
between a Swift implementation and a Rust implementation for deterministic
computation. Classification and fingerprinting are two examples. Both legs
must produce the exact same answer from the exact same input.

Binding a socket and parsing HTTP headers is operating-system transport
glue. It is not that kind of deterministic computation, so it falls outside
the parity rule. The Rust side of MOOTx01's MCP transport, ARIA_MCP-rust,
hand-rolls its own separate networking code. A project rule forbids that
code from calling into Swift or C libraries across a foreign-function
boundary. The two sides only have to agree at the JSON-RPC message level,
not at the socket level.

## How It Works

The library is organized in two layers, one file each.

`POSIXSocket.swift` is the transport layer. It opens a listening socket
bound to `127.0.0.1` on a chosen TCP port. It can also open a Unix-domain
socket at a filesystem path locked to owner-only access. It accepts one
incoming connection at a time. It reads or writes raw bytes on the
resulting connection.

`HTTPWire.swift` is the protocol layer, built on top of the transport.
`HTTPRequest.read(fd:)` reads raw bytes from a connected socket. It parses
them into a structured request: a method, a path, a query string, a table
of headers, and a body. `HTTPResponse` is a value the caller builds: a
status code, headers, and a body. The library serializes that value into
correct HTTP/1.1 wire bytes and writes it back to the socket. `SSEStream`
frames the narrower SSE wire format. It writes one head line once, then any
number of `data:` frames over time, on a connection the caller keeps open.

A typical request passes through the library in one direction only. Bytes
arrive from `POSIXSocket`. `HTTPRequest.read` turns them into a request
value. The caller inspects that value and decides what to do. The caller's
resulting `HTTPResponse` or `SSEStream` frames go back out through
`POSIXSocket`. Nothing loops back. No component here holds state between one
request and the next connection.

That simple, one-way shape is why this library needs no larger topology
diagram. It is two files, a handful of value types, and a straight line from
incoming bytes to outgoing bytes.

## What Ships in the Package

The package ships two Swift source files and no bundled resources,
artifacts, or Rust port. `Package.swift` declares one library product,
`LoopbackHTTP`, with no external dependencies. It targets macOS 26 and iOS
26.

The package also builds on Linux Swift. `POSIXSocket.swift` uses a
compile-time import guard that selects `Glibc` there and `Darwin` on Apple
platforms.
