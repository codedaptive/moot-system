---
doc: AGENT_MAP
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

# AGENT_MAP: ObserverSink

PURPOSE: reusable PersistenceKit-backed telemetry sink for the MOOTx01 manager pipeline (Manager 1.0, Phase 0.5). IntellectusLib.report(_:) → PersistenceStatsSink.receive(_:) → flag-gated SQLite write into StatsStore (metric_samples / event_samples). Also owns topology_snapshots (governor write, dashboard read) and store-level retention.

DEPS: imports IntellectusLib (StatsSink protocol, StatSample/EventKind datum), PersistenceKit + PersistenceKitSQLite (Storage/SchemaDeclaration/RowStore, SQLiteStorage backend), Foundation, OSLog. Dependency hierarchy (no inversion, per MANAGER_1.0_PLAN.md §4): IntellectusLib (floor) → PersistenceKit (kit) → ObserverSink (this lib). Imported by: none within the moot-system repo at this commit: Package.swift comments name the intended consumers (Intellectus install site in AriaResident/aria-mcp, moot-mgr manager) as living outside this repo. Rust port in rust/ (crate `observer-sink`) mirrors schema + flag semantics exactly; conformance tests in Tests/ObserverSinkTests/ObserverSinkConformanceTests.swift and rust/tests/conformance.rs gate parity (16 scenarios).


CURRENT TRUE-UP:
- v1.0.24: `PersistenceStatsSink` caps in-flight writes at 64. `StatsStore.schemaVersion` is 5. Monitoring defaults on with a source marker. Composite metric index `(dropbox_id, name, ts)` backs latest-value and aggregate dashboard queries.

ENTRY POINTS (most callers need only these):
- PersistenceStatsSink.swift:102 `PersistenceStatsSink.init(store:dropboxID:)`: construct the sink; store must already be `.open()`ed
- PersistenceStatsSink.swift:123 `receive(_ sample: StatSample)`: StatsSink conformance; called by Intellectus.report(_:), never call directly
- StatsStore.swift:362 `StatsStore.init(url:) throws`: construct the store
- StatsStore.swift:384 `open() async throws`: apply schema/migrations + seed control rows (idempotent)

## Symbol Table

- StatsStore.swift `queryMetricsByNames(_:dropboxID:limit:)`: optional bounded newest-first query; nil limit preserves ascending full history.

### Sink: PersistenceStatsSink.swift
- :77 `struct PersistenceStatsSink: StatsSink`: Sendable; all mutable state lives in StatsStore, not here
- :82 `store: StatsStore` (private)
- :89 `dropboxID: String` (private): tags every written row; identifies source consumer/process
- :91 `logger` (private): `Logger(subsystem: "com.mootx01.kit", category: "ObserverSink")`
- :102 `init(store:dropboxID:)`: see ENTRY POINTS
- :123 `receive(_:)`: synchronous, non-blocking; dispatches unstructured Task; inside: isMonitoringEnabled() gate → insertMetric/insertEvent by StatSample case → catch-and-log, never throws/propagates

### Schema constants: StatsStore.swift `enum StatsStoreSchema`
- :49 `enum StatsStoreSchema`: table/column name constants; typo → compile error, not silent empty query
- :54 `metricSamplesTable = "metric_samples"` / :57 `eventSamplesTable = "event_samples"` / :60 `controlTable = "control"` / :117 `topologySnapshotsTable = "topology_snapshots"`
- :65 `tsColumn = "ts"` (TEXT ISO-8601 on every table) / :68 `dropboxIDColumn = "dropbox_id"`
- :73 `nameColumn`, :76 `valueColumn` (REAL), :79 `tagsColumn` (TEXT JSON): metric_samples
- :84 `kindColumn` (EventKind raw string), :87 `nounTypeColumn` (INTEGER ordinal), :91 `rowIDColumn = "estate_row_id"` (NOT the synthetic PK), :94 `estateColumn`: event_samples
- :99 `keyColumn` (PK), :102 `controlValueColumn`: control
- :107 `monitoringKey = "monitoring"` (value "1"/"0") / :112 `retentionCutoffKey = "retention_cutoff"` (ISO-8601, default epoch-zero)
- :120 `generatedAtColumn`, :123 `payloadColumn`, :132 `topologyFingerprintColumn` (nullable, v3): topology_snapshots

### Store: StatsStore.swift `final class StatsStore: Sendable`
- :160 `final class StatsStore: Sendable`: wraps SQLiteStorage
- :164 `storage: SQLiteStorage` (private) / :165 `logger` (private)
- :175 `static let schemaVersion = 3`: v1: metric/event/control; v2: +topology_snapshots; v3: +topology_fingerprint (nullable)
- :193 `static let schema: SchemaDeclaration`: 4 tables, 2 indices (ts on metric_samples/event_samples), 2 migrations (v1→v2 createTable, v2→v3 addColumn)
- :317 migrations block: v1→v2 :321, v2→v3 :340; SQLite backend creates fresh DBs at latest version directly (migrations replay only matters for InMemory/upgrade path)
- :362 `init(url:) throws`: fresh random estateID per StatsStore instance; no I/O
- :384 `open() async throws`: applies schema, then :396/:398 `seedControlIfAbsent` for monitoring="0" and retention_cutoff=epoch-zero; SEED-IF-ABSENT not upsert
- :412 `seedControlIfAbsent(key:value:)` (private): existence check then insert; preserves operator-set values across reopen
- :433 `close() async`: idempotent
- :448 `isMonitoringEnabled() async throws -> Bool`: control["monitoring"]=="1"; missing row → false (safe default)
- :470 `setMonitoringEnabled(_:) async throws`: upsert on keyColumn; manager-only caller by convention (sink never writes this)
- :496 `insertMetric(name:value:tags:ts:dropboxID:) async throws`: assigns row_id UUID; tags→JSON via encodeTagsJSON; ts(Double epoch)→.timestamp(Date) at this boundary
- :532 `insertEvent(kind:nounType:rowID:estate:ts:dropboxID:) async throws`: rowID stored in estate_row_id column, NOT row_id (that's the synthetic PK)
- :576 `writeTopologySnapshot(estate:generatedAt:payload:fingerprint:) async throws`: upsert keyed on estate (latest-wins, no history); throws StorageError.invalidQuery if payload isn't valid UTF-8; fingerprint nil → .null column
- :615 `latestTopologySnapshot(estate:) async throws -> Data?`: estate=nil → newest generated_at across ALL estates (dashboard "all" view); uses :679 generatedAtInstant to tolerate both .timestamp and .text read-back reps (SQLite vs InMemory): DO NOT match only .timestamp, that was a real bug (all rows tie at .distantPast under SQLite)
- :655 `loadTopologyFingerprint(estate:) async throws -> String?`: nil if no snapshot / pre-v3 row / written without fingerprint
- :679 `generatedAtInstant(_:) -> Date` (private): see above
- :696 `queryMetrics(dropboxID:) async throws -> [MetricRow]`: full-table (optionally dropbox-filtered) read, ts ascending
- :725 `queryMetricsByNames(_:dropboxID:) async throws -> [MetricRow]`: `WHERE name IN (...)`; empty names → [] immediately; USE THIS not queryMetrics+filter on hot read paths
- :769 `countMetrics() async throws -> Int`: COUNT(*), no row decode
- :784 `queryEvents(dropboxID:) async throws -> [EventRow]`: full-table (optionally dropbox-filtered) read, ts ascending
- :818 `deleteMetricsBefore(cutoff:now:) async throws -> Int`: ts < cutoff; updates retention_cutoff control row to `cutoff` (not `now`)
- :842 `deleteEventsBefore(cutoff:now:) async throws -> Int`: same semantics, event_samples
- :873 `storageStats(now:) async throws -> StorageStats?`: store's OWN SQLite backend health (WAL frames, file size, page/freelist counts), not an observed estate's storage; via StorageIntrospection, SQLite backend always returns non-nil
- :883 `recordRetentionCutoff(_:now:) async throws` (private): upserts retention_cutoff as ISO-8601
- :907 `static let iso8601Formatter: DateFormatter`: "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'", UTC, en_US_POSIX; matches SQLiteStorage's timestamp codec
- :924 `encodeTagsJSON(_:) -> String` (private): `.sortedKeys` for determinism; empty/unencodable → "{}"
- :939 `static func decodeTagsJSON(_:) -> [String: String]`: parse failure → [:] (forward-compatible)

### Row types
- :951 `struct MetricRow: Sendable`: rowID/name/value/tags/ts/dropboxID; :966 `init?(storageRow:)` returns nil on any column type mismatch (compactMap-friendly)
- :986 `struct EventRow: Sendable`: rowID/kind/nounType/rowIDStr/estate/ts/dropboxID; :1003 `init?(storageRow:)` same nil-on-mismatch contract

### Rust port (rust/, crate observer_sink): mirrors above 1:1 except where Rust semantics differ
- rust/src/sink.rs `PersistenceStatsSink::receive`: INLINE synchronous I/O (no Task/thread spawn); Rust StatsSink trait is sync and SqliteStorage is Mutex-backed
- rust/src/store.rs `StatsStore::SCHEMA_VERSION = 3`, `make_schema()`: line-by-line mirror of Swift `StatsStore.schema`
- rust/src/store.rs `epoch_to_iso8601` / `iso8601_to_epoch` (crate-internal): explicit ISO-8601 codec (no DateFormatter equivalent); clamps NaN/±inf/out-of-range to year 0001–9999 window, never panics (unit-tested at bottom of store.rs)
- rust/src/store.rs `write_topology_snapshot_bytes`: Rust-only convenience: lossy UTF-8 conversion of raw bytes; Swift has no equivalent because Foundation's `String(data:encoding:)` guard is a runtime check callers already handle inline

## INVARIANTS / GOTCHAS

- Seed-if-absent, NEVER upsert, for control-row defaults in `open()`. Upserting monitoring on every open would reset an operator's "on" back to "off" on every process restart. This exact bug was fixed in Swift commit 852821cc (see rust/src/store.rs comment); do not reintroduce it in either port.
- `isMonitoringEnabled()` fails safe: missing row → `false` (off), never assume on.
- `PersistenceStatsSink` only READS the monitoring flag; only the manager process calls `setMonitoringEnabled`. Do not add a write path to the sink.
- `receive(_:)` MUST stay synchronous and non-blocking (StatsSink protocol contract). All I/O happens off the calling thread/task (Swift: unstructured Task; Rust: inline sync, which is fine because Rust's I/O is not on an async hot path here).
- No in-process buffering at v1.0 by design: one Task per sample in Swift. Do not add batching without also updating OVERVIEW/DETAILS and the design comment in PersistenceStatsSink.swift.
- `event_samples.estate_row_id` is NOT the table's primary key: `row_id` (synthetic UUID) is the PK; `estate_row_id` carries the estate's own UUID for that row. Never conflate the two when reading/writing.
- Retention (`deleteMetricsBefore`/`deleteEventsBefore`) takes `cutoff` AND `now` as caller-supplied parameters. NO `Date()` / `SystemTime::now()` call inside either engine: determinism contract, both ports.
- `recordRetentionCutoff` stores `cutoff` (the boundary applied), not `now` (when the pass ran): intentional, dashboard-facing choice.
- `latestTopologySnapshot`/`latest_topology_snapshot` newest-across-estates comparison MUST tolerate both `.timestamp` and `.text` read-back representations for `generated_at` (SQLite returns text, InMemory returns timestamp). Matching only one representation reintroduces a real, previously-shipped bug where SQLite rows all tie at `.distantPast` / `i64::MIN` and an arbitrary row wins.
- `topology_snapshots` is latest-wins per estate (PRIMARY KEY = estate): no history retained. This is intentional; do not add versioning without a schema bump and a doc update.
- `topology_fingerprint` column is nullable (v3, additive). Absent/null means "no fingerprint computed yet," not an error: callers (autonomic governor) treat null as "recompute once."
- Timestamps are TEXT (ISO-8601 UTC, millisecond precision) on every table. No REAL/numeric timestamp column exists anywhere in this schema. Swift boundary: `Date(timeIntervalSince1970: ts)` at insert, `iso8601Formatter` for retention-cutoff string formatting. Rust boundary: `epoch_to_iso8601`/`iso8601_to_epoch`.
- Rust's `epoch_to_iso8601` clamps to the year 0001–9999 window and never panics on NaN/±infinity/out-of-range f64: required because a malformed 5-digit or negative year would break every lexicographic TEXT comparison retention and topology-snapshot ordering rely on. Six unit tests in rust/src/store.rs lock this behavior; do not remove them when refactoring.
- Tag maps: encoded with `.sortedKeys` (Swift) / serialized from a `BTreeMap` (Rust) for deterministic JSON: needed for reproducible test fixtures, not for correctness of the round trip itself. Decode failure → empty map in both ports (forward-compatible, never throws).
- `queryMetricsByNames` is the required hot-path read API: issues `WHERE name IN (...)`; do not reintroduce `queryMetrics` + Swift-side filtering on a read path that matters for latency.
- No pinned data artifacts in this package (unlike LatticeLib-style libs): every table starts empty; there is nothing to version-check against a build-time asset.
- Both-ports parity is enforced ONLY by the two conformance test suites (Tests/ObserverSinkTests + rust/tests/conformance.rs), not by any shared fixture file. Changing schema, flag semantics, or timestamp encoding in one leg requires updating the other leg AND both suites by hand.
