---
doc: OVERVIEW
package: QueueKit
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
sources:
  - path: Sources/QueueKit/DrainLease.swift
    blob: 3f1df6aa1fab44c993bd49d96de179ff03450303
  - path: Sources/QueueKit/FilesystemBackend.swift
    blob: c0e5f536e20b1def11875e57b587e8392b6f936d
  - path: Sources/QueueKit/Job.swift
    blob: 5f39e97a8113e47de2f2a18563446072de8ec6cf
  - path: Sources/QueueKit/ObservationStatus.swift
    blob: a32b0e8c93311ce498d12ffb4df864a2652c707e
  - path: Sources/QueueKit/PersistenceKitBackend.swift
    blob: 96175a8b27c8b6e929f417d95b141f727c5179d7
  - path: Sources/QueueKit/QueueBackend.swift
    blob: eb67e0b821bc9bfdec8cfde621c5e70d4295d331
  - path: Sources/QueueKit/QueueError.swift
    blob: 2697c4b7404b9e04259267cd8f4008030ebb6754
  - path: Sources/QueueKit/QueueKit.swift
    blob: 60dfaa1e8f92ec051810b50d8b9cadc47388c02f
  - path: Sources/QueueKit/QueueKitTelemetry.swift
    blob: 29024f112bf133012283205175aa336b8d80d7c9
  - path: Sources/QueueKit/Watcher.swift
    blob: cf2b270b9c60da34f7a25c016f8c18b6ba6149e4
---

# QueueKit Overview

## What This Library Does

QueueKit is a general-purpose work queue. It lets one part of MOOTx01 hand a
unit of work to another part without the two parts running in the same
process, or even at the same moment. MOOTx01 is an on-device AI memory
system. It stores what an AI observes over time and helps the AI recall it
later. Many of its jobs — encoding a memory, running a dreaming pass over the
estate, replaying a signal — are best done off to the side, by a separate
worker, so the part of the system that noticed the work does not have to wait
for it to finish.

QueueKit is a Kit. A Kit is a larger package that composes libraries into a
subsystem; kits may depend on libraries, never the reverse. QueueKit lives in
moot-system, the repository that houses the kits that hold MOOTx01 together
at the system level.

The unit of work is called a `Job`. A job carries an identifier, the name of
the stream it belongs to, a submission timestamp, a priority, and an
arbitrary payload of bytes. A producer calls `send` to hand a job to the
queue. A consumer calls `drain` to claim available jobs, does the work, and
calls `reply` to mark each job finished. QueueKit exposes exactly four
permanent methods for this cycle — `send`, `drain`, `watch`, `reply` — no
matter which storage mechanism backs the queue underneath.

## The Problem It Solves

Handing work across a process boundary is harder than it looks. Three
requirements make it hard, and QueueKit exists to meet all three at once.

First, the handoff must survive a crash. If the producing process, the
queue itself, or the consuming process dies partway through, no job may be
silently lost or silently claimed twice. A job is claimed exactly once at a
time, and a completed job leaves a durable record of how it finished.

Second, independent workloads must not interfere with each other while
sharing one queue. An encode worker and a dreaming worker might both draw
from the same estate's queue, but neither should be able to claim, or block
on, the other's jobs. QueueKit calls a workload a stream, and every
operation has a stream-scoped form so that consumers can share one queue
without stepping on each other.

Third, the format on disk or in the database must mean the same thing no
matter which programming language wrote or reads it. MOOTx01 ships Swift
code for Apple devices, Rust code for cross-platform daemons and services,
and Python code for command-line tooling. A job enqueued by one language's
producer must be decodable, byte for byte, by another language's consumer.
QueueKit therefore treats its wire format — the exact bytes a job or a
completion signal is encoded as — as a contract, gated by conformance
fixtures shared across all three implementations.

## How It Works

QueueKit separates the fixed four-method interface from the storage
mechanism underneath it. A backend is anything that conforms to the
`QueueBackend` protocol: it can write a job, claim the available jobs, watch
for new ones, and mark a job complete. The public `QueueKit` class does not
know or care which backend it holds; it forwards every call and adds only
the telemetry and the await-drain convenience described below.

Two backends ship today. `FilesystemBackend` needs no database. It lays out
four subdirectories under a root — `tmp`, `new`, `cur`, `done` — following
the maildir layout that mail servers such as Postfix have used for decades
to hand off messages between processes without a lock file: a job is written
into `tmp`, then atomically renamed into `new`; claiming it is another atomic
rename into `cur`; finishing it renames it into `done`. Every step relies on
the filesystem's own atomicity guarantee, so two processes racing to claim
the same job can never both win.

`PersistenceKitBackend` stores jobs as rows in a table inside PersistenceKit,
MOOTx01's storage kit. It suits an estate that already keeps a shared,
encrypted SQLite database open for other purposes. Claiming a batch of jobs
here is one guarded database transaction that flips every available row from
`new` to `cur` under a single session identifier, then reads back exactly the
rows that transaction claimed — an approach that scales as one pass over the
table rather than one query per job.

Every job carries a submission timestamp built from a Hybrid Logical Clock,
or HLC — a clock design that combines wall-clock time with a small counter
so that events from different machines or processes still sort into one
consistent, gap-free order, even when the machines' clocks disagree slightly.
QueueKit uses the HLC ordering to decide which job is oldest, both when a
consumer wants first-in-first-out delivery and when telemetry reports how
long the oldest waiting job has been sitting in the queue.

Two supporting mechanisms keep multi-process operation honest. `DrainLease`
is a heartbeat lock, keyed by stream, that lets many processes share one
estate while guaranteeing that only one of them actively drains a given
stream at a time; a stale lease (its holder has not renewed it recently) is
automatically taken over. `Watcher` gives the filesystem backend an
efficient way to notice new work without spinning a loop: it uses the
kqueue mechanism on Apple platforms, inotify on Linux, and a plain
200-millisecond poll everywhere else, falling back automatically if the
faster mechanism cannot be set up.

## How the Pieces Fit

Figure 1 shows the library's topology — its major parts and how a job moves
through them.

![Figure 1. Topology of QueueKit](topology.svg)

*Figure 1. Topology of QueueKit. A producer calls the `QueueKit` facade,
which delegates to whichever backend is mounted. Dashed regions mark
external kit boundaries — the encrypted database PersistenceKit owns, and
the telemetry sink IntellectusLib owns — plus the maildir directories that
are the filesystem backend's own on-disk state.*

A caller mounts `QueueKit` once, over either backend, and afterward talks
only to the facade. `send` and the batch form `send(batch:)` hand jobs to
the backend. `drain` and the stream-scoped `drain(stream:)` claim available
jobs and return them together with a session identifier that groups
everything claimed in that one call. `watch` subscribes a handler that fires
for every job as it becomes available, driven by `Watcher` on the filesystem
backend and by a database change observer on the PersistenceKit backend.
`reply` — in its single-job, per-session, and per-batch forms — marks
claimed jobs finished with a terminal status and, on the filesystem backend,
writes a durable signal file recording how the job ended.

`awaitDrain` is a convenience built on top of the four permanent methods
rather than a new backend capability: it polls the pending count and the
in-flight count until both reach zero, so a bulk caller — an importer
finishing a batch, a test verifying a pipeline emptied — can wait for every
enqueued job to finish without wiring its own polling loop.

Telemetry is additive and off by default. When enabled, `QueueKitTelemetry`
reports queue depth, drain throughput, claim latency percentiles, and the
age of the oldest waiting job to IntellectusLib, MOOTx01's self-report
telemetry library, tagged by estate so a fleet of estates can be watched
side by side.

## What Ships in the Package

The package ships ten Swift source files implementing the facade, the two
backends, and their supporting infrastructure; a Rust port in `rust/` (eight
source files, roughly three thousand lines) that reimplements both backends
for non-Apple hosts; and a Python port in `python/queuekit/` that
reimplements the filesystem backend only, for command-line tooling that has
no need of a database backend. A shared set of conformance fixtures under
`Tests/QueueKitTests/Fixtures/` — recorded job and completion-signal
input/output pairs — gates all three implementations to the same wire
format, byte for byte.
