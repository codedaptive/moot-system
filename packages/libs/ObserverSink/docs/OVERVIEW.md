---
doc: OVERVIEW
package: ObserverSink
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
sources:
  - path: Sources/ObserverSink/PersistenceStatsSink.swift
    blob: e76c55599795f4bc85b860a4901dc9ab04dd201f
  - path: Sources/ObserverSink/StatsStore.swift
    blob: 483c736feffd82821a8d77030afde0e182d320fc
---

# ObserverSink Overview

## What This Library Does

ObserverSink records telemetry for the MOOTx01 manager pipeline. Telemetry
is data that describes what a running program is doing, separate from the
memories the program stores. A companion library, IntellectusLib, defines
two shapes of telemetry: a metric (a named number, such as a latency
measurement) and an event (a record that one memory row was captured or
otherwise acted on). IntellectusLib defines only the shapes and a plug-in
point; it does not know how to save anything to disk.

ObserverSink is the plug-in that saves them. It ships two pieces. The
first, `PersistenceStatsSink`, is the receiver that IntellectusLib calls
each time new telemetry arrives. The second, `StatsStore`, is a small
SQLite database and the code that reads and writes it. Together they turn
a stream of in-memory telemetry values into durable rows that a separate
manager process can later query.

## The Problem It Solves

A running MOOTx01 process is a black box unless something records what it
does. An operator who wants to know how fast captures are running, how
many think-cycles fired, or whether a given estate is behaving normally
needs that information written somewhere durable, because the process
itself may restart or be inspected only after the fact. An estate is one
user's complete memory store in MOOTx01.

Writing telemetry to disk on every observation is expensive if done
carelessly, and it is dangerous if it cannot be turned off. ObserverSink
solves both problems with one mechanism: a flag row in the database
itself. Before writing anything, the sink checks whether monitoring is
switched on. The manager — not the process being observed — owns that
switch, so it can enable or disable recording for every consumer at once,
without restarting anything. This is the flag-row signal mechanism
described in the source comments as Bob's confirmed choice for Manager 1.0.

The library also has to work without slowing down the code it observes.
`receive(_:)`, the method IntellectusLib calls, must return immediately
because it may be called from time-sensitive code paths. ObserverSink
meets this requirement by never performing file I/O on the calling
thread; the actual database write always happens elsewhere.

## How It Works

Each telemetry value follows the same path. IntellectusLib calls
`PersistenceStatsSink.receive(_:)` with one `StatSample`, which is either
a `.metric` case or an `.event` case. The sink captures what it needs and
starts an unstructured background task, then returns immediately. The
calling code never waits for the database write to finish.

Inside that background task, the sink first asks the store whether
monitoring is enabled. This is a database read, but a cheap one: the
`control` table holds only a handful of rows. If the flag is off, the
sample is dropped and nothing else happens. If it is on, the sink asks the
store to serialize the sample into the correct table: `.metric` samples go
into `metric_samples`, `.event` samples go into `event_samples`. If the
write fails for any reason, the error is logged and swallowed — a
telemetry failure must never crash the program it is describing.

The `StatsStore` also owns two other kinds of data beyond metrics and
events. A `control` table holds the monitoring flag itself, plus a
timestamp recording when data was last rolled off through retention. A
`topology_snapshots` table holds one row per estate: the latest picture of
that estate's structure, written by a separate background process (the
autonomic governor) and served back verbatim to dashboards that ask for
it. This table is not telemetry in the metric/event sense, but it shares
the same store, the same open/migrate machinery, and the same principle:
one writer, many readers, no history kept beyond the latest value.

Retention keeps the two sample tables from growing without bound. The
manager periodically calls `deleteMetricsBefore(cutoff:now:)` and
`deleteEventsBefore(cutoff:now:)` with a cutoff timestamp it computes
itself; the store never reads the system clock on its own. This keeps the
store's behavior fully determined by its caller's inputs, which matters
for testing and for reasoning about what a given retention pass will do.

## How the Pieces Fit

Figure 1 shows the library's topology — its major parts and how data moves
between them.

![Figure 1. Topology of ObserverSink](topology.svg)

*Figure 1. Topology of ObserverSink. A telemetry sample flows from the
caller through the sink's background task, past the monitoring-flag gate,
into the matching SQLite table. A separate flow lets the autonomic
governor publish topology snapshots that a dashboard reads back later.*

`Intellectus.report(_:)`, part of IntellectusLib, is the only caller of
`PersistenceStatsSink.receive(_:)`. The sink holds a reference to a
`StatsStore` and a `dropboxID` string that tags every row it writes with
the identity of the process that produced it — this lets one shared store
serve many concurrent consumers (for example, several `aria-mcp` process
instances) without their rows becoming mixed up. SQLite's write-ahead log
mode makes concurrent writers from separate consumers safe without extra
locking in this library.

`StatsStore` wraps a `SQLiteStorage` value supplied by PersistenceKit, a
kit that provides the schema-declaration and row-storage machinery this
library builds on. `StatsStore` never talks to SQLite directly; it
describes its four tables as a `SchemaDeclaration` and lets PersistenceKit
create, migrate, and query them.

## What Ships in the Package

The package ships two Swift source files — `PersistenceStatsSink.swift`
and `StatsStore.swift` — plus a Rust port in `rust/` that reimplements the
same schema and the same sink logic for non-Swift consumers. Both legs are
exercised by parallel conformance test suites (`Tests/ObserverSinkTests`
in Swift, `rust/tests/conformance.rs` in Rust) that check the same sixteen
scenarios: schema version, control-row seeding, the monitoring flag,
metric and event round-trips, retention roll-off, tag encoding, and the
topology-snapshot read/write paths. There are no pinned data artifacts in
this package; every table starts empty and is populated only by the
processes that use it at run time.
