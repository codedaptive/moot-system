---
doc: DETAILS
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

# ObserverSink Details

This document walks through both source files in the package. Read
`OVERVIEW.md` first for the big picture. `PersistenceStatsSink.swift`
comes first because it is the entry point every caller uses;
`StatsStore.swift` follows because it is what the sink calls into.

## PersistenceStatsSink.swift

This file provides `PersistenceStatsSink`, the concrete implementation of
IntellectusLib's `StatsSink` protocol. It is the only type in the package
that a typical caller touches directly.

A `StatsSink` is any type that can receive one `StatSample` at a time.
IntellectusLib defines the protocol and a global installation point;
`PersistenceStatsSink` is the implementation that actually saves samples
by writing them into a `StatsStore`. A host program constructs one sink,
installs it with `Intellectus.install(sink:)`, and turns monitoring on
with `Intellectus.setEnabled(true)`. From that point on, every call to
`Intellectus.report(_:)` anywhere in the process reaches this sink's
`receive(_:)` method.

The struct holds three pieces of state: the `store` it writes to, a
`dropboxID` string identifying this consumer, and a `Logger` for
diagnostics. All three are `let` constants, and the struct conforms to
`Sendable` — required because `StatsSink` values are held in a
process-wide global and called from any thread. No mutable state lives in
the sink itself; anything that changes over time (the monitoring flag, the
stored rows) lives inside `StatsStore`, which manages its own
concurrency.

`init(store:dropboxID:)` is a plain constructor. It does not open the
store — the caller must call `store.open()` first, because opening
involves asynchronous I/O that a synchronous initializer cannot perform.
This split between construction and opening matters when a host wants to
retry a failed open without recreating the sink.

`receive(_:)` is the method that matters. It is declared as a synchronous,
non-blocking function because the `StatsSink` protocol documents that
requirement: whatever calls `Intellectus.report(_:)` must not stall
waiting for a database write. To honor that, `receive(_:)` captures the
store, the dropbox ID, and the logger — each `Sendable` — and immediately
starts an unstructured `Task`. The method then returns; the caller never
waits.

Inside the `Task`, the real work happens in three steps. First, it calls
`store.isMonitoringEnabled()`. This is the flag-row check described in
`OVERVIEW.md`: a cheap read of one row in the `control` table. If
monitoring is off, the sample is discarded and a debug-level log line is
written — debug level specifically, because this check runs on every
single sample when monitoring is off, and a louder log level would flood
the console. Second, if monitoring is on, the code switches on the
`StatSample` case: a `.metric` sample calls `store.insertMetric(...)`, an
`.event` sample calls `store.insertEvent(...)`. Third, the whole block is
wrapped in a `do`/`catch`; any thrown error — from the flag read or from
either insert — is logged at `.error` level and never rethrown. A
telemetry sink that could crash the process it is monitoring would defeat
its own purpose, so every failure mode here ends in a log line, never a
propagated error.

The file's header comments record a deliberate scope limit for this
version: there is no in-process buffering. Each sample launches its own
`Task`, and SQLite's write-ahead log mode is relied on to make concurrent
writes from many such tasks safe and reasonably efficient. The comments
name a possible future improvement — batching many samples behind a timer
— and explain why it was not built yet: stats recording sits off the
hot substrate path, so an occasional dropped sample under heavy load is an
acceptable cost, and the simpler design is easier to reason about.

## StatsStore.swift

This file provides `StatsStore`, the SQLite-backed component that owns the
telemetry schema, and `StatsStoreSchema`, a namespace of string constants
that name its tables and columns.

### Schema constants — `StatsStoreSchema`

Every table name and column name that any part of this package touches is
declared once, as a `public static let`, inside `StatsStoreSchema`. The
file's own comment explains the reason: gathering the strings in one place
turns a typo into a compile error instead of a query that silently matches
zero rows. `monitoringKey` and `retentionCutoffKey` additionally name the
two well-known rows inside the `control` table, since that table is a
generic key-value store rather than one column per concept.

### Schema declaration — `StatsStore.schema`

`StatsStore.schema` is a `SchemaDeclaration` value — a data structure from
PersistenceKit that describes tables, columns, indices, and migrations
without executing any SQL directly. `StatsStore.schemaVersion` (currently
`3`) is the version number PersistenceKit checks against this
declaration's migrations to decide whether an existing database needs to
be upgraded.

The schema declares four tables. `metric_samples` and `event_samples` hold
the telemetry rows this library exists to record; both use a
`.uuid("row_id")` synthetic primary key that the store assigns, so callers
never have to generate or track one. `control` is the key-value table for
the monitoring flag and the retention-cutoff timestamp; its primary key is
the `key` column itself, which is what lets `setMonitoringEnabled` use an
upsert rather than a delete-then-insert. `topology_snapshots`, added in
schema version 2, holds one row per estate — its primary key is the
`estate` column, so writing a new snapshot for an estate automatically
replaces the old one instead of accumulating history.

Two indices — `idx_metric_samples_ts` and `idx_event_samples_ts` — exist
purely to make retention fast. Retention deletes rows with `ts` below a
cutoff; without an index on `ts`, each retention pass would scan the
entire table.

Two migrations are declared, one per schema version bump.
`fromVersion: 1, toVersion: 2` adds the `topology_snapshots` table; the
comment notes this is purely additive; no existing row is touched, and the
new table starts empty until the autonomic governor's next duty cycle
populates it. `fromVersion: 2, toVersion: 3` adds the nullable
`topology_fingerprint` column to that same table, again additively — any
row written before this version simply reads back with that column `nil`.

### Lifecycle — `init`, `open()`, `close()`

`init(url:)` builds the underlying `SQLiteStorage` for a database file at
`url`, generating a fresh random estate identifier for the storage
configuration. It performs no I/O beyond what opening a SQLite connection
requires.

`open()` does two things: it asks PersistenceKit's storage layer to apply
`StatsStore.schema` (creating tables and running any pending migrations),
then it seeds two default rows in the `control` table — `"monitoring"` set
to `"0"` and `"retention_cutoff"` set to the epoch-zero ISO-8601 string.
Both seeds go through `seedControlIfAbsent(key:value:)`, a private helper
that checks whether a row with that key already exists before inserting.
This distinction — seed-if-absent rather than upsert — matters because the
monitoring flag is meant to be a persistent switch: if `open()` blindly
overwrote it every time, an operator's choice to turn monitoring on would
be silently reset to off on every process restart. Seeding only when
absent makes the first `open()` call install the defaults and every later
call on the same database a no-op for those two rows.

`close()` closes the underlying storage. It is documented as idempotent —
safe to call more than once — which matters for callers that close a store
in both a normal shutdown path and a `defer` block.

### Monitoring flag — `isMonitoringEnabled()`, `setMonitoringEnabled(_:)`

`isMonitoringEnabled()` reads the single `control` row whose key is
`"monitoring"` and returns `true` only if its value is the string `"1"`.
If the row is missing entirely — for instance, if a caller queries a store
that was never opened — the function returns `false`. This fail-safe
default matters because a missing flag should never be interpreted as
"recording is on."

`setMonitoringEnabled(_:)` writes `"1"` or `"0"` to that same row using an
upsert keyed on the `key` column, so it works whether or not the row
already exists. The manager process is the only intended caller of this
method; `PersistenceStatsSink` only ever reads the flag, never sets it.
This asymmetry is what lets one manager control every consumer's recording
behavior without touching the consumers themselves.

### Writing samples — `insertMetric(...)`, `insertEvent(...)`

`insertMetric(name:value:tags:ts:dropboxID:)` inserts one row into
`metric_samples`. It generates the row's UUID primary key itself, encodes
the `tags` dictionary to a JSON string with `encodeTagsJSON(_:)`, and
converts the caller-supplied `ts` — epoch seconds, a `Double` — to a
`Date` for storage. The schema stores timestamps as ISO-8601 text rather
than a native numeric column; the conversion from epoch seconds happens
here, at the boundary between the caller's representation and the store's,
so every other part of the codebase can treat `ts` as a plain number.

`insertEvent(kind:nounType:rowID:estate:ts:dropboxID:)` does the same for
`event_samples`. Its `rowID` parameter is stored in the `estate_row_id`
column rather than a column literally named `row_id`, because that name is
already taken by the table's own synthetic primary key; the file's
comments call this out explicitly so a future reader does not confuse the
estate's row identifier with the row's own storage identifier.

### Topology snapshots — `writeTopologySnapshot(...)`, `latestTopologySnapshot(estate:)`, `loadTopologyFingerprint(estate:)`

`writeTopologySnapshot(estate:generatedAt:payload:fingerprint:)` upserts
one row per estate into `topology_snapshots`, keyed on the `estate`
column, so a new snapshot always replaces the previous one for that
estate rather than accumulating history. The `payload` parameter is raw
`Data` that must decode as UTF-8 text; the function throws if it does not,
because the autonomic governor that calls this method always produces
valid UTF-8 JSON, and a failure here signals a bug worth surfacing rather
than silently storing garbage. The optional `fingerprint` parameter is a
stable, process-independent hash of the topology inputs; when supplied, it
lets a restarting governor compare against a freshly computed fingerprint
and skip re-reading the estate's full set of drawers, tunnels, and facts
when nothing has changed. When omitted, the column is written as `nil`.

`latestTopologySnapshot(estate:)` reads the payload back. Passing a
specific estate identifier is a primary-key lookup that returns at most
one row. Passing `nil` instead asks for the single newest snapshot across
every estate in the table — the behavior the dashboard's "all estates"
view relies on, since it has no single estate identifier to filter by.
Comparing "newest" requires care: the column is declared as a timestamp,
but PersistenceKit's SQLite backend reads timestamp columns back as plain
text, while its in-memory backend used in tests reads them back as native
timestamp values. `generatedAtInstant(_:)` is a small private helper that
normalizes either representation to a `Date` before comparing, because an
earlier version of this code matched only the timestamp representation,
which meant every row tied under SQLite and an arbitrary one won — a bug
the in-memory-backed tests could not catch because they never exercised
this mismatch.

`loadTopologyFingerprint(estate:)` is the read side of the fingerprint
parameter described above: a primary-key lookup that returns `nil` when no
snapshot exists yet, when the row predates schema version 3, or when a
snapshot was written without a fingerprint.

### Reading samples — `queryMetrics`, `queryMetricsByNames`, `queryEvents`, `countMetrics`

`queryMetrics(dropboxID:)` and `queryEvents(dropboxID:)` return every row
in their respective tables, optionally filtered to one consumer's
dropbox, ordered by timestamp ascending. Both decode raw storage rows into
the public `MetricRow` and `EventRow` structs, skipping any row that fails
to decode rather than throwing — a defensive choice that tolerates a
malformed row without losing the rest of the query's results.

`queryMetricsByNames(_:dropboxID:)` exists for a specific reason: reading
every metric row and filtering client-side does not scale once the table
holds many distinct metric names. This method issues a `WHERE name IN
(...)` predicate so the database itself narrows the result set, and it
short-circuits to an empty array without querying at all when the caller
passes an empty name set. The method's documentation says explicitly that
hot read paths should use this instead of `queryMetrics` plus a Swift-side
filter.

`countMetrics()` answers "how many metric rows exist" with a `COUNT(*)`
query that never decodes a single row. It backs a dashboard total-count
display without paying the cost of reading and parsing every row just to
throw away everything except the count.

### Retention — `deleteMetricsBefore(cutoff:now:)`, `deleteEventsBefore(cutoff:now:)`

Both methods delete rows whose `ts` is strictly before a caller-supplied
`cutoff`, then update the `retention_cutoff` control row through the
private `recordRetentionCutoff(_:now:)` helper. Neither method reads the
system clock; `cutoff` and `now` both come from the caller. This
determinism rule — no `Date()` calls inside the engine — is what lets a
test construct an exact, reproducible retention scenario instead of racing
against wall-clock time. The stored cutoff records the boundary that was
applied, not the moment retention ran, because a dashboard showing "data
older than this point was removed" is more useful to an operator than a
timestamp of when the deletion happened to execute.

### DB-layer health — `storageStats(now:)`

`storageStats(now:)` reports statistics about the stats store's own SQLite
file — page counts, WAL frame counts, file size — as distinct from any
statistics about an observed estate's own storage. It delegates to
PersistenceKit's `StorageIntrospection` capability, which the SQLite
backend implements directly, so the call always succeeds in practice; the
optional return type exists only to keep the API honest for a
hypothetical backend that does not support introspection. The `now`
parameter follows the same determinism rule as retention: the caller
supplies the timestamp to stamp on the snapshot.

### JSON tag encoding — `encodeTagsJSON(_:)`, `decodeTagsJSON(_:)`

Metric tags are a flat `[String: String]` map, stored as a JSON text
column rather than a separate table, because the tag set is small and
simple enough that a second table would add complexity without adding
value. `encodeTagsJSON(_:)` encodes with `.sortedKeys` so that the same
tag map always produces the same JSON string — useful for tests and for
any future deduplication logic that might compare encoded rows.
`decodeTagsJSON(_:)` is forward-compatible by design: a string that fails
to parse decodes to an empty dictionary rather than throwing, so a future
change to the tag encoding cannot break old rows written before the
change.

### Row result types — `MetricRow`, `EventRow`

Both are plain `Sendable` structs that mirror a decoded database row.
Their failable initializers, `init?(storageRow:)`, pattern-match every
expected column and its expected `TypedValue` case; if any column is
missing or has an unexpected type, the initializer returns `nil` rather
than crashing. This is what lets `queryMetrics` and `queryEvents` use
`compactMap` to silently drop malformed rows instead of failing the whole
query.

## Rust Port and Conformance

The `rust/` directory contains a second implementation of this library:
`src/lib.rs` re-exports the public surface, `src/sink.rs` implements
`PersistenceStatsSink`, and `src/store.rs` implements `StatsStore` and
`StatsStoreSchema` against the Rust build of PersistenceKit. The two ports
share table names, column names, and the monitoring-flag semantics
exactly; the schema-declaration code in `store.rs`'s `make_schema()`
function is a line-by-line mirror of the Swift `StatsStore.schema` value.

The two legs differ where Rust and Swift semantics genuinely differ,
rather than translating one language's idioms into the other
mechanically. `PersistenceStatsSink::receive` in Rust performs its
database write inline, synchronously, because the Rust `StatsSink` trait
is synchronous and Rust's `SqliteStorage` is safely accessible without an
async runtime; the Swift version's `Task` dispatch exists only because
Swift's `Storage` protocol is actor-isolated and asynchronous. The
monitoring-flag check and the discard-on-off behavior are identical in
both.

The Rust `store.rs` file also owns timestamp conversion explicitly through
two free functions, `epoch_to_iso8601` and `iso8601_to_epoch`, because Rust
has no equivalent to Foundation's `DateFormatter` built in. These functions
clamp out-of-range or non-finite inputs (`NaN`, infinities, values outside
the year 0001–9999 window) to the nearest valid boundary instead of
panicking or producing malformed text; a block of unit tests at the bottom
of `store.rs` checks every one of these edge cases directly, since a
malformed ISO-8601 string would break every string comparison retention
and topology-snapshot ordering depend on. The Rust store also exposes one
convenience method the Swift store does not need,
`write_topology_snapshot_bytes`, which accepts raw bytes and performs a
lossy UTF-8 conversion — useful because Rust's type system otherwise
forces a caller to prove UTF-8 validity before calling the primary method,
a proof Swift's runtime guard does not require its callers to construct in
advance.

Conformance between the two ports is enforced by two independent but
matching test suites: `Tests/ObserverSinkTests/ObserverSinkConformanceTests.swift`
and `rust/tests/conformance.rs`. Both exercise the same scenarios — schema
version, control-row seeding, the monitoring flag and its persistence
across a close-and-reopen cycle, metric and event round-trips, retention
roll-off for both tables, tag JSON round-tripping, the topology-snapshot
read/write/latest-wins/per-estate-isolation paths, and the DB-layer health
check — over the same table and column names. When you change either leg's
schema or flag semantics, update both files and both test suites; nothing
in either build enforces this automatically.
