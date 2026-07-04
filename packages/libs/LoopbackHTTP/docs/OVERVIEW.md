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

LoopbackHTTP is a small HTTP/1.1 server primitive. It listens on a socket, reads
one request, and lets the caller write one response. It also knows how to
frame a Server-Sent-Events (SSE) stream, a way for a server to push a
continuing series of small text messages to a browser over one open
connection. The library does not decide what any of that traffic means; it
only moves the bytes correctly.

The library binds strictly to the loopback address, `127.0.0.1`. A loopback
address is one that only the same machine can reach; no other computer on the
network can connect to it. Every listener this library creates is reachable
only from processes running on the same device.

## The Problem It Solves

Two programs inside MOOTx01 need a local HTTP server: the moot-mgr monitor
daemon, which serves a small dashboard, and the resident mootx01 MCP daemon,
which serves the Model Context Protocol used by AI tool integrations. Before
this library existed, each program hand-rolled its own socket and parsing
code. Two hand-rolled copies drift apart over time: one gets a security fix,
the other does not; one handles a header correctly, the other does not.
LoopbackHTTP was extracted from moot-mgr, under decision record
ADR-LOOPBACKHTTP-001, so both programs share one audited implementation
instead of maintaining two.

The library adds no external package dependency. It wraps only the system C
socket API supplied by the operating system (`libc` on Linux, `Darwin` on
Apple platforms). A larger, general-purpose HTTP package such as SwiftNIO was
available but was rejected: it would pull in a dependency graph broader than
this narrow need justifies, and the moot-system kit rules ask each library to
justify every dependency it takes. Even Apple's own `Network.framework` was
tried and rejected for a concrete reason: its listener type refused to bind a
listening server socket in this project's command-line build environment,
failing with a low-level error code on every configuration tested. Plain
POSIX sockets bind correctly in that same environment, so the library builds
on them directly.

The library is deliberately authentication-free. It exposes two convenience
readers on a parsed request, `bearerToken` and `origin`, so a caller can read
those header values easily. But the library itself never decides whether a
request is authorized. That decision is composed above the transport, one
layer up, by each consumer: the Community Edition of moot-mgr enforces no
policy at all, the full moot-mgr enforces a bearer token plus an Origin
header check, and an Enterprise Edition-only remote layer enforces OAuth
2.1. Keeping the transport policy-free lets the identical compiled library
ship inside every edition unchanged; only the layer above it differs.

The library is Swift-only. MOOTx01 keeps a byte-for-byte parity discipline
between a Swift implementation and a Rust implementation for deterministic
computation, such as classification or fingerprinting, where both legs must
produce the exact same answer from the exact same input. Binding a socket and
parsing HTTP headers is operating-system transport glue, not that kind of
deterministic computation, so it falls outside the parity rule. The Rust side
of MOOTx01's MCP transport (ARIA_MCP-rust) hand-rolls its own separate
networking code under a rule that forbids it from calling into Swift or C
libraries across a foreign-function boundary; the two sides only have to
agree at the JSON-RPC message level, not at the socket level.

## How It Works

The library is organized in two layers, one file each.

`POSIXSocket.swift` is the transport layer. It opens a listening socket bound
to `127.0.0.1` on a chosen TCP port, or opens a Unix-domain socket at a
filesystem path locked to owner-only access. It accepts one incoming
connection at a time and reads or writes raw bytes on the resulting
connection.

`HTTPWire.swift` is the protocol layer, built on top. `HTTPRequest.read(fd:)`
reads raw bytes from a connected socket and parses them into a structured
request: a method, a path, a query string, a table of headers, and a body.
`HTTPResponse` is a value the caller builds — a status code, headers, and a
body — which the library then serializes into correct HTTP/1.1 wire bytes and
writes back to the socket. `SSEStream` frames the narrower SSE wire format:
one head line written once, then any number of `data:` frames written over
time on a connection the caller keeps open.

A typical request passes through the library in one direction only: bytes
arrive from `POSIXSocket`, `HTTPRequest.read` turns them into a request value,
the caller inspects that value and decides what to do, and the caller's
resulting `HTTPResponse` or `SSEStream` frames go back out through
`POSIXSocket`. Nothing loops back, and no component here holds state between
one request and the next connection. That simple, one-way shape is why this
library needs no larger topology diagram: two files, a handful of value
types, and a straight line from incoming bytes to outgoing bytes.

## What Ships in the Package

The package ships two Swift source files and no bundled resources, artifacts,
or Rust port. `Package.swift` declares one library product, `LoopbackHTTP`,
with no external dependencies, targeting macOS 26 and iOS 26 while also
building on Linux Swift through a compile-time import guard in
`POSIXSocket.swift` that selects `Glibc` there and `Darwin` on Apple
platforms.
