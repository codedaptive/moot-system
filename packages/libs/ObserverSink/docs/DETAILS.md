---
doc: DETAILS
package: ObserverSink
repo: moot-system
authored_commit: f1c1f3bf8dafd26faf5df26c2ddf2ea909e2df18
authored_date: 2026-07-23
sources:
  - path: Sources/ObserverSink/PersistenceStatsSink.swift
    blob: 103d3c53ca69aaef22d4066f11b2655ddb944252
  - path: Sources/ObserverSink/StatsStore.swift
    blob: d6d9888777838655164ae6c56b6b85577d8e3bca
---

# ObserverSink Details

## Current Release Details

Monitoring now has a source marker.
Fresh stores seed monitoring as on with source `default`.
A user toggle writes source `user`.
A later open then preserves that choice.
Older stores with missing source data are migrated carefully.

Schema v5 adds the composite metric index `(dropbox_id, name, ts)`.
`queryLatestMetricsByNamesAndDropboxes` uses that index for latest-value reads.
`queryMetricAggregatesByDropbox` uses it for dashboard counts and timestamps.

`queryMetricsByNames` now accepts an optional limit.
With a limit it orders by newest timestamp first.
Without a limit it keeps the earlier ascending full-history behavior.
This gives callers a bounded latest-value path.

This document walks through both source files in the package. Read
`OVERVIEW.md` first for the big picture. `PersistenceStatsSink.swift`
comes first, because it is the entry point every caller uses.
`StatsStore.swift` follows, because it is what the sink calls into.

## PersistenceStatsSink.swift

This file provides `PersistenceStatsSink`. It is the concrete
implementation of IntellectusLib's `StatsSink` protocol, and the only
type in the package a typical caller touches directly.

A `StatsSink` is any type that can receive one `StatSample` at a time.
IntellectusLib defines the protocol and a global installation point.
`PersistenceStatsSink` is the implementation that actually saves
samples, by writing them into a `StatsStore`. A host program constructs
one sink. It installs the sink with `Intellectus.install(sink:)`. It then
turns monitoring on with `Intellectus.setEnabled(true)`. From that point
on, every call to `Intellectus.report(_:)` anywhere in the process
reaches this sink's `receive(_:)` method.

The struct holds three pieces of state. It holds the `store` it writes
to, a `dropboxID` string that identifies this consumer, and a `Logger`
for diagnostics. All three are `let` constants. The struct conforms to
`Sendable`, a requirement because `StatsSink` values live in a
process-wide global and get called from any thread. No mutable state
lives in the sink itself. Anything that changes over time, such as the
monitoring flag or the stored rows, lives inside `StatsStore`. `StatsStore`
manages its own concurrency.

`init(store:dropboxID:)` is a plain constructor. It does not open the
store. The caller must call `store.open()` first, because opening
involves async I/O that a synchronous initializer cannot perform. This
split between construction and opening matters when a host wants to
retry a failed open without recreating the sink.

`receive(_:)` is the method that matters. The `StatsSink` protocol
documents a requirement: whatever calls `Intellectus.report(_:)` must
never stall waiting for a database write. So `receive(_:)` is declared as
a synchronous, non-blocking function. To honor that requirement,
`receive(_:)` captures three values first. It captures the store, the
dropbox ID, and the logger. Each value is `Sendable`. The method then
starts a background task and returns. The caller never waits.

Inside the task, the real work happens in three steps. First, the code
calls `store.isMonitoringEnabled()`. This is the flag-row check described
in `OVERVIEW.md`: a cheap read of one row in the `control` table. If
monitoring is off, the sample is discarded, and a debug-level log line is
written. The level is debug specifically, because this check runs on
every single sample when monitoring is off. A louder log level would
flood the console. Second, if monitoring is on, the code switches on the
`StatSample` case. A `.metric` sample calls `store.insertMetric(...)`. An
`.event` sample calls `store.insertEvent(...)`. Third, the whole block
sits inside a `do`/`catch`. Any thrown error, whether from the flag read
or from either insert, gets logged at `.error` level and never rethrown.
A telemetry sink that could crash the process it monitors would defeat
its own purpose. So every failure mode here ends in a log line, never a
propagated error.

The file's header comments record a deliberate scope limit for this
version. There is no in-process buffering. Each sample launches its own
task. SQLite's write-ahead log mode keeps concurrent writes from many
such tasks safe and reasonably efficient. The comments name a possible
future improvement: batching many samples behind a timer. They also
explain why that improvement was not built yet. Stats recording sits off
the hot substrate path. An occasional dropped sample under heavy load is
an acceptable cost there. The simpler design is also easier to reason
about.

## StatsStore.swift

`queryMetricsByNames` filters one set of names.
It can also filter one dropbox.
The optional limit changes the query to newest-first order.
This avoids loading all matching history for latest-value callers.

This file provides `StatsStore`, the SQLite-backed component that owns
the telemetry schema. It also provides `StatsStoreSchema`, a namespace of
string constants that name the tables and columns.

### Schema Constants: `StatsStoreSchema`

Every table name and column name that this package touches gets declared
once, as a `public static let`, inside `StatsStoreSchema`. The file's own
comment explains the reason. Gathering the strings in one place turns a
typo into a compile error, instead of a query that silently matches zero
rows. `monitoringKey` and `retentionCutoffKey` additionally name the two
well-known rows inside the `control` table. That table is a generic
key-value store, rather than one column per concept.

### Schema Declaration: `StatsStore.schema`.

`StatsStore.schema` is a `SchemaDeclaration` value. This value comes from
PersistenceKit. It describes tables, columns, indices, and migrations. It
never runs any SQL directly. `StatsStore.schemaVersion` is currently
three. PersistenceKit checks this number against the declaration's
migrations. This check decides whether an existing database needs an
upgrade.

The schema declares four tables. `metric_samples` and `event_samples`
hold the telemetry rows this library exists to record. Both use a
`.uuid("row_id")` synthetic primary key that the store assigns, so
callers never have to generate or track one. `control` is the key-value
table for the monitoring flag and the retention-cutoff timestamp. Its
primary key is the `key` column itself. That design is what lets
`setMonitoringEnabled` use an upsert, rather than a delete followed by an
insert. `topology_snapshots`, added in schema version two, holds one row
per estate. Its primary key is the `estate` column. Writing a new
snapshot for an estate automatically replaces the old one. History never
accumulates.

Two indices exist purely to make retention fast: `idx_metric_samples_ts`
and `idx_event_samples_ts`. Retention deletes rows with `ts` below a
cutoff. Without an index on `ts`, each retention pass would scan the
entire table.

Two migrations are declared, one per schema version bump. The migration
from version one to version two adds the `topology_snapshots` table. The
comment notes that this change is purely additive. No existing row is
touched, and the new table starts empty until the autonomic governor's
next duty cycle fills it. The migration from version two to version three
adds the nullable `topology_fingerprint` column to that same table, again
additively. Any row written before this version simply reads back with
that column set to `nil`.

### Lifecycle: `init`, `open()`, `close()`

`init(url:)` builds the underlying `SQLiteStorage` for a database file at
`url`. It generates a fresh random estate identifier for the storage
configuration. It performs no I/O beyond what opening a SQLite connection
requires.

`open()` does two things. First, it asks PersistenceKit's storage layer
to apply `StatsStore.schema`, creating tables and running any pending
migrations. Second, it seeds two default rows in the `control` table.
`"monitoring"` gets set to `"0"`, and `"retention_cutoff"` gets set to the
epoch-zero ISO-8601 string. Both seeds go through
`seedControlIfAbsent(key:value:)`, a private helper that checks whether a
row with that key already exists before inserting. This seed-if-absent
approach, rather than an upsert, matters for one reason. The monitoring
flag is meant to be a persistent switch. Suppose `open()` blindly
overwrote it every time. An operator's choice to turn monitoring on
would then reset to off silently, on every process restart. Seeding only
when the row is absent avoids this. The first `open()` call installs the
defaults. Every later call on the same database becomes a no-op for those
two rows.

`close()` closes the underlying storage. It is documented as idempotent,
meaning it is safe to call more than once. That property matters for
callers that close a store in both a normal shutdown path and a `defer`
block.

### Monitoring Flag: `isMonitoringEnabled()`, `setMonitoringEnabled(_:)`

`isMonitoringEnabled()` reads the single `control` row whose key is
`"monitoring"`. It returns `true` only if that row's value is the string
`"1"`. If the row is missing entirely, for instance if a caller queries a
store that was never opened, the function returns `false`. This
fail-safe default matters. A missing flag should never read as
"recording is on."

`setMonitoringEnabled(_:)` writes `"1"` or `"0"` to that same row, using
an upsert keyed on the `key` column. So it works whether or not the row
already exists. The manager process is the only intended caller of this
method. `PersistenceStatsSink` only ever reads the flag; it never sets
it. This asymmetry is what lets one manager control every consumer's
recording behavior, without touching the consumers themselves.

### Writing Samples: `insertMetric(...)`, `insertEvent(...)`

`insertMetric(name:value:tags:ts:dropboxID:)` inserts one row into
`metric_samples`. It generates the row's UUID primary key itself. It
encodes the `tags` dictionary to a JSON string with `encodeTagsJSON(_:)`.
It converts the caller-supplied `ts`, epoch seconds as a `Double`, into a
`Date` for storage. The schema stores timestamps as ISO-8601 text, not as
a native numeric column. The conversion from epoch seconds happens here,
at the boundary between the caller's representation and the store's. So
every other part of the codebase can treat `ts` as a plain number.

`insertEvent(kind:nounType:rowID:estate:ts:dropboxID:)` does the same job
for `event_samples`. Its `rowID` parameter is stored in the
`estate_row_id` column. It is not stored in a column literally named
`row_id`. That name is already taken by the table's own synthetic primary
key. The file's comments call this out explicitly. A future reader
should not confuse the estate's row identifier with the row's own
storage identifier.

### Topology Snapshots: `writeTopologySnapshot(...)`, `latestTopologySnapshot(estate:)`, `loadTopologyFingerprint(estate:)`.

`writeTopologySnapshot(estate:generatedAt:payload:fingerprint:)` upserts
one row per estate into `topology_snapshots`, keyed on the `estate`
column. So a new snapshot always replaces the previous one for that
estate, instead of accumulating history. The `payload` parameter is raw
`Data` that must decode as UTF-8 text. The function throws if it does
not, because the autonomic governor that calls this method always
produces valid UTF-8 JSON. A failure here signals a bug worth surfacing,
rather than silently storing garbage. The optional `fingerprint`
parameter is a stable, process-independent hash of the topology inputs.
When supplied, it lets a restarting governor compare against a freshly
computed fingerprint. The governor can then skip re-reading the estate's
full set of drawers, tunnels, and facts when nothing has changed. When
the parameter is omitted, the column is written as `nil`.

`latestTopologySnapshot(estate:)` reads the payload back. Passing a
specific estate identifier is a primary-key lookup that returns at most
one row. Passing `nil` instead asks for the single newest snapshot across
every estate in the table. This is the behavior the dashboard's "all
estates" view relies on, since that view has no single estate identifier
to filter by. Comparing "newest" requires care. The column is declared as
a timestamp, but PersistenceKit's SQLite backend reads timestamp columns
back as plain text. Its in-memory backend, used in tests, reads them back
as native timestamp values instead. `generatedAtInstant(_:)` is a small
private helper that normalizes either representation to a `Date` before
comparing. An earlier version of this code matched only the timestamp
representation. Under that earlier version, every row tied under SQLite.
An arbitrary row won each time. This was a bug. The in-memory-backed
tests could not catch it, because they never exercised this mismatch.

`loadTopologyFingerprint(estate:)` is the read side of the fingerprint
parameter described above. It is a primary-key lookup. The lookup returns
`nil` in three cases: no snapshot exists yet, the row predates schema
version three, or a snapshot was written without a fingerprint.

### Reading Samples: `queryMetrics`, `queryMetricsByNames`, `queryEvents`, `countMetrics`.

`queryMetrics(dropboxID:)` and `queryEvents(dropboxID:)` return every row
in their respective tables. Each call can optionally filter to one
consumer's dropbox, and results come back ordered by timestamp ascending.
Both decode raw storage rows into the public `MetricRow` and `EventRow`
structs. Both skip any row that fails to decode. Neither throws. This is
a defensive choice. It tolerates a malformed row without losing the rest
of the query's results.

`queryMetricsByNames(_:dropboxID:)` exists for a specific reason. Reading
every metric row and filtering it on the client side does not scale. It
breaks down once the table holds many distinct metric names. This method
issues a `WHERE name IN (...)` predicate. The database itself then
narrows the result set. The method also short-circuits to an empty
array, with no query at all, when the caller passes an empty name set.
The method's documentation states explicitly that hot read paths should
use this, instead of `queryMetrics` plus a Swift-side filter.

`countMetrics()` answers "how many metric rows exist" with a `COUNT(*)`
query. This query never decodes a single row. It backs a dashboard
total-count display. It never pays the cost of reading and parsing every
row just to throw away everything except the count.

### Retention: `deleteMetricsBefore(cutoff:now:)`, `deleteEventsBefore(cutoff:now:)`

Both methods delete rows whose `ts` sits strictly before a
caller-supplied `cutoff`. Both then update the `retention_cutoff` control
row through the private `recordRetentionCutoff(_:now:)` helper. Neither
method reads the system clock. `cutoff` and `now` both come from the
caller. This determinism rule matters. The engine never calls `Date()`
internally. A test can then build an exact, reproducible retention
scenario. The test never has to race against wall-clock time. The stored
cutoff records the boundary that was applied. It does not record the
moment retention ran. A dashboard can then show "data older than this
point was removed." That message is more useful to an operator than a
timestamp of when the deletion happened to execute.

### DB-Layer Health: `storageStats(now:)`

`storageStats(now:)` reports statistics about the stats store's own
SQLite file: page counts, WAL frame counts, file size. These are distinct
from any statistics about an observed estate's own storage. The method
delegates to PersistenceKit's `StorageIntrospection` capability, which
the SQLite backend implements directly. So the call always succeeds in
practice. The optional return type exists only to keep the API honest for
a hypothetical backend that does not support introspection. The `now`
parameter follows the same determinism rule as retention. The caller
supplies the timestamp to stamp on the snapshot.

### JSON Tag Encoding: `encodeTagsJSON(_:)`, `decodeTagsJSON(_:)`

Metric tags form a flat `[String: String]` map. The store keeps this map
as a JSON text column, not a separate table. The tag set is small and
simple. A second table would add complexity without adding value. `encodeTagsJSON(_:)` encodes with `.sortedKeys`, so
the same tag map always produces the same JSON string. This helps tests,
and it helps any future deduplication logic that might compare encoded
rows. `decodeTagsJSON(_:)` is forward-compatible by design. A string that
fails to parse decodes to an empty dictionary, rather than throwing. So a
future change to the tag encoding cannot break old rows written before
the change.

### Row Result Types: `MetricRow`, `EventRow`

Both are plain `Sendable` structs that mirror a decoded database row.
Their failable initializers, `init?(storageRow:)`, pattern-match every
expected column and its expected `TypedValue` case. If any column is
missing, or has an unexpected type, the initializer returns `nil` rather
than crashing. This is what lets `queryMetrics` and `queryEvents` use
`compactMap` to silently drop malformed rows, instead of failing the
whole query.

## Rust Port and Conformance

The `rust/` directory contains a second implementation of this library.
`src/lib.rs` re-exports the public surface. `src/sink.rs` implements
`PersistenceStatsSink`. `src/store.rs` implements `StatsStore` and
`StatsStoreSchema` against the Rust build of PersistenceKit. The two
ports share table names, column names, and the monitoring-flag semantics
exactly. The schema-declaration code in `store.rs`'s `make_schema()`
function is a line-by-line mirror of the Swift `StatsStore.schema` value.

The two legs differ only where Rust and Swift semantics genuinely
differ, rather than translating one language's idioms into the other
mechanically. `PersistenceStatsSink::receive` in Rust performs its
database write inline and synchronously. The Rust `StatsSink` trait is
synchronous, and Rust's `SqliteStorage` is safely accessible without an
async runtime. The Swift version's task dispatch exists only because
Swift's `Storage` protocol is actor-isolated and asynchronous. The
monitoring-flag check and the discard-on-off behavior stay identical in
both languages.

The Rust `store.rs` file also owns timestamp conversion explicitly,
through two free functions: `epoch_to_iso8601` and `iso8601_to_epoch`.
Rust has no equivalent to Foundation's `DateFormatter` built in. Some
inputs are unsafe on their own, such as `NaN`, infinities, and
out-of-range years. The valid year window runs from 0001 to 9999. These
functions clamp unsafe values to the nearest valid boundary in that
window. They never panic. They never produce malformed text. A block of
unit tests at the bottom of `store.rs` checks every one of
these edge cases directly. A malformed ISO-8601 string would break every
string comparison that retention and topology-snapshot ordering depend
on. The Rust store also exposes one convenience method the Swift store
does not need: `write_topology_snapshot_bytes`. This method accepts raw
bytes and performs a lossy UTF-8 conversion. It is useful because Rust's
type system otherwise forces a caller to prove UTF-8 validity before
calling the primary method. Swift's runtime guard does not require its
callers to construct that proof in advance.

Conformance between the two ports is enforced by two independent but
matching test suites: `Tests/ObserverSinkTests/ObserverSinkConformanceTests.swift`
and `rust/tests/conformance.rs`. Both exercise the same scenarios over the
same table and column names. These scenarios include the schema version
and control-row seeding. They include the monitoring flag, plus its
persistence across a close-and-reopen cycle. They include metric and
event round-trips, retention roll-off for both tables, and tag JSON
round-tripping. They include the topology-snapshot read, write, and
latest-wins paths too. They include per-estate isolation and the
DB-layer health check as well. Changing either leg's schema or flag
semantics requires updating both files and both test suites by hand.
Nothing in either build enforces this automatically.
