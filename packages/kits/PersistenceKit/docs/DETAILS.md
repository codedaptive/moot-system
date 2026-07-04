---
doc: DETAILS
package: PersistenceKit
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
sources:
  - path: Sources/PersistenceKit/AuditLog.swift
    blob: ca4c0c9623056a49ad888a7a77e720a7519556bb
  - path: Sources/PersistenceKit/BlobStore.swift
    blob: f052c3ecc693d5414d57ad2c6da5fb4c5fe28f79
  - path: Sources/PersistenceKit/CacheInvalidator.swift
    blob: 0a844b8037404dd72d08df6887ceee1e8c6014f2
  - path: Sources/PersistenceKit/CachingRowStore.swift
    blob: 7edf9a86eb31dd9a17abd97b7baa9e8d5425e266
  - path: Sources/PersistenceKit/Column.swift
    blob: 3cfc3ba856ebe15e8756ae65dae736cbd2c288a6
  - path: Sources/PersistenceKit/EncryptionMode.swift
    blob: fdb7aa7ec63088ae09739f5e2a8e1468c386b987
  - path: Sources/PersistenceKit/ErasureLedger.swift
    blob: eb2ca35f69783eead6049949bbaad68decfc9915
  - path: Sources/PersistenceKit/ErasureOverlay.swift
    blob: d5a86f2baa10647134f285d6996a8e62cbe23ace
  - path: Sources/PersistenceKit/EstateCacheConfig.swift
    blob: 7003951cb5b2b075b6bb0c49b5fe372a97bfeb3a
  - path: Sources/PersistenceKit/EstateConfiguration.swift
    blob: be91569405e5c48a99859b4595540a59bd1a1994
  - path: Sources/PersistenceKit/GCPin.swift
    blob: 73336dc4c693fcfa285a7eff8ed6520f27951ff8
  - path: Sources/PersistenceKit/GeneratedColumn.swift
    blob: 34e466c80a0e9aac5d24303eaad132901333470b
  - path: Sources/PersistenceKit/HashingRowStore.swift
    blob: fe233ed2f4fda177373ad105d14fa321d325d0df
  - path: Sources/PersistenceKit/NoOpObserver.swift
    blob: 99f62b6889df50c5f09fce749f363637891f116c
  - path: Sources/PersistenceKit/NovelTokenTaggerChoice.swift
    blob: 9588d6303431883a2c8e0ee727f7ebe3e3f6d139
  - path: Sources/PersistenceKit/PersistenceKitTelemetry.swift
    blob: a24e54dfb97beb86dac9422bbfc7cc3c2d959d73
  - path: Sources/PersistenceKit/Predicate.swift
    blob: 522802c842dfc7573137efd7a3f36300d5201468
  - path: Sources/PersistenceKit/RowCrypto.swift
    blob: 6678e426c0902cdae6d164904001526756e40916
  - path: Sources/PersistenceKit/RowStore.swift
    blob: 9a96ff89528ca786d51224324cacb78cf810037d
  - path: Sources/PersistenceKit/Schema.swift
    blob: 5a6894c68d6f29eecfe9acc34d7c06ee2a655ac3
  - path: Sources/PersistenceKit/SnapshotRegistry.swift
    blob: 88087bda544165c4d24c514a4f3e0641a620512e
  - path: Sources/PersistenceKit/Storage.swift
    blob: 7484a40913b28cb11a6bd2e3ea822dc8fe8eb63e
  - path: Sources/PersistenceKit/StorageError.swift
    blob: 743c2d1a24c7bafedb217e4c6bbf30ca20d03be8
  - path: Sources/PersistenceKit/StorageIntrospection.swift
    blob: 35522b6601037246907bb88ebb2fb2eb5ea9b0e2
  - path: Sources/PersistenceKit/StorageObserver.swift
    blob: f0a8b61a15344e02193137a3393be345dd51cf25
  - path: Sources/PersistenceKit/Transaction.swift
    blob: d58d478618b31cc3985275be6ceee84a6fc222de
  - path: Sources/PersistenceKit/TypedValue.swift
    blob: ec124b7aebd86f64f67fea6656396d374666b741
  - path: Sources/PersistenceKitInMemory/InMemoryAuditLog.swift
    blob: 1ba83d2408d3384d4d8e4f286fa2db743a433cc9
  - path: Sources/PersistenceKitInMemory/InMemoryBlobStore.swift
    blob: e02822bde83c899b6d18ab8d82b90afba6abb5ac
  - path: Sources/PersistenceKitInMemory/InMemoryObserver.swift
    blob: 71b98c9da2d869a81a78265b7ef1f64a57a907af
  - path: Sources/PersistenceKitInMemory/InMemoryRowStore.swift
    blob: 2f7c92612c46b8a097e4211147c924f8356e47d1
  - path: Sources/PersistenceKitInMemory/InMemoryStorage.swift
    blob: 9820c771bfe39ab738eea1f3b1623491fcfb1326
  - path: Sources/PersistenceKitInMemory/PredicateEvaluator.swift
    blob: e8735422ff87addff6a2a3a89da85c495d07f07c
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLConnection.swift
    blob: 5a282a6064a69a543a5d650ccc7eeeae2c5a3e4f
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLIdentifierValidator.swift
    blob: 120cf0c1576a7db45d79bf6865e2f15cff09a5ac
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLPool.swift
    blob: eddc0c3af4135565e15d2d763b0d24240973dd6f
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLPredicateCompiler.swift
    blob: 6f0791e0372b09cf2605bae4cf149e48bbba2834
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLSchema.swift
    blob: f165f0877b96c87e2f7de46012f8f8acb3c03cc8
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLStorage.swift
    blob: 849926f9e6def788fae2f38757ad44bcd52a18a0
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLStores.swift
    blob: bff90838d1324d82ae76a0895f15399537e9e343
  - path: Sources/PersistenceKitReplication/IncrementalReplicationSession.swift
    blob: 2f90378146e043d75c739d400d98c7770e07af78
  - path: Sources/PersistenceKitReplication/ReplicationTypes.swift
    blob: bb13c63d1febd50ad46c1814610d7f6c31a33112
  - path: Sources/PersistenceKitReplication/StorageReplicator.swift
    blob: f5acc5993c8647e53c127c6871a1282c24b1c427
  - path: Sources/PersistenceKitSQLite/KeychainKeyStore.swift
    blob: 0071732291a7cb6ce0777bd230a6188276fb4f32
  - path: Sources/PersistenceKitSQLite/SQLiteConnection.swift
    blob: ece56dc7e25e67656bb37f5222c18c1166c750cc
  - path: Sources/PersistenceKitSQLite/SQLiteIdentifierValidator.swift
    blob: 713339c137d6af1cfbba5a3584e05bdda70b42c5
  - path: Sources/PersistenceKitSQLite/SQLiteObserver.swift
    blob: 2cb61ed75dae5ab81a3cdfb5c9581731d25fd960
  - path: Sources/PersistenceKitSQLite/SQLitePredicateCompiler.swift
    blob: 5770adb2024c54ea6921651632ca65ee84d416af
  - path: Sources/PersistenceKitSQLite/SQLiteSchema.swift
    blob: 4ba6fc175fe9d486b17af1088a23da64041b12ae
  - path: Sources/PersistenceKitSQLite/SQLiteStorage.swift
    blob: 417e63cc07b60295ac874187ebb796bf9f3b3c86
  - path: Sources/PersistenceKitSQLite/SQLiteStores.swift
    blob: 76499ffb70d979f0d13e8cf9e32bc38ff28ffdb5
---

# PersistenceKit Details

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear grouped by target,
in the order a reader should learn them: the core protocols and value
types first, then the decorators and cross-cutting toolkits built on top
of them, then the three backends that implement the core protocols, and
finally the replication module that operates across two backends.

## Target: PersistenceKit (core)

### Storage.swift

This file provides the `Storage` protocol — the single entry point every
backend implements. A term of art first: a protocol in Swift is a contract
that a type promises to fulfill; any type that conforms to `Storage` can
be used anywhere `Storage` is expected, regardless of what physical
engine backs it.

`Storage` bundles four sub-stores — `rowStore`, `blobStore`, `auditLog`,
`observer` — plus five life-cycle functions: `open(schema:)` creates files
or connections and brings the backend up to its declared schema version;
`close()` shuts it down cleanly and must be safe to call more than once;
`transaction(isolation:_:)` runs a block of work atomically, rolling back
every change if the block throws; and the two `currentSchemaVersion`
overloads report how far a backend's schema has migrated, either
globally or for one named kit. `migrate(to:)` advances the schema forward.
A protocol extension supplies a default isolation level of read-committed,
so most callers can write `storage.transaction { ... }` without naming a
level at all.

### RowStore.swift

This file provides the typed row input/output protocol, plus three small
supporting types: `RowKey` (a type alias for `UUID`), `StorageRow` (an
immutable, subscriptable wrapper around a `[String: TypedValue]` row), and
`RowHandle` (a `(table, key)` pair that identifies exactly one row).

`RowStore` declares the operations every backend must support: `insert`,
`upsert`, `update`, `delete`, `query`, and `count`. Two further query
variants matter for performance and safety. `query(...columns:)` is the
column-projecting form: passing a specific list of column names means a
column such as a large text blob is never transferred out of storage at
all, which matters when a caller only needs a handful of small columns
from a wide table. `querySkipCorrupt(...)` is the resilience form: rather
than aborting an entire corpus scan the moment one row's stored value
fails to parse, it skips that row, counts it, and returns everything
else — the file's documentation is explicit that this method exists for
best-effort scans (like "every drawer in the estate"), never for a
single-row point lookup, where a corrupt value should always be a loud
error.

A protocol extension supplies default implementations for all of the
above (falling back to the plain, unprojected `query`), plus a family of
as-of temporal query overloads. As-of querying means asking what a row
looked like at a past point in time, identified by an `AsOfCoordinate`
(a type defined in the `SubstrateTypes` dependency). The default
implementation of the as-of overloads answers `.present` queries normally
and throws `StorageError.featureGated` for any `.asOf(hlc)` request — the
feature is deliberately turned off until two other in-flight pieces of
work (lineage-wide expunge and the erasure overlay) both ship, because
turning it on early could let an as-of read resurface content that was
supposed to have been erased. Finally, this file declares the write-
transaction boundary — `beginTransaction`, `commitTransaction`,
`rollbackTransaction` — as protocol requirements rather than
protocol-extension defaults. This distinction matters for a subtle
Swift reason: a protocol requirement dispatches to the concrete type even
when called through an `any RowStore` existential, while a
protocol-extension default does not always do so reliably. Because
`CachingRowStore` must forward these calls to whatever it wraps, they
have to be true requirements.

### BlobStore.swift

This file provides the blob input/output protocol: `put`, `get`,
`delete`, `exists`, `size`, and `listKeys`. A blob is a chunk of raw
bytes identified by an arbitrary string key — typically a
content-addressed hash or a row's UUID combined with a column name.
`listKeys()` exists specifically so the replication primitive can
enumerate every blob in a backend for a full-snapshot copy; because
key order is unspecified and may differ between backends and even
between calls, any caller that needs a stable order (replication does)
sorts the returned keys itself.

### AuditLog.swift

This file provides the append-only history protocol. An audit log
records every change made to a row, in order, and never edits or removes
a past entry. `append` and `appendBatch` are both idempotent on the
compound key `(eventID, hlc)` — HLC stands for Hybrid Logical Clock, a
timestamp format that orders events consistently even across multiple
devices without perfectly synchronized clocks — so replaying the same
event twice (which can happen during sync) never creates a duplicate.
`iterate(after:rowID:limit:)` walks the log in HLC order and accepts a
resume cursor, and `eventsForRow(_:)` returns just one row's history for
building a point-in-time projection of that row. PersistenceKit's role
stops at durable, ordered storage of these events; enforcing the
conflict-resolution rules of a CRDT (a data structure designed so
concurrent edits always merge the same way) belongs to a higher kit.

### StorageObserver.swift

This file provides the live change-notification protocol, plus four
supporting event types. `StorageEvent` is the three kinds of row change
(`insert`, `update`, `delete`); `TableChange` bundles one such event with
its table, row key, values, and HLC. `BlobEvent` and `BlobChange` are the
blob-store analogues — a `put` event carries the written bytes so a
subscriber (specifically, the incremental replication session) can avoid
a second round-trip just to re-read what was written. `DirtyChainEvent`
is a third, more specialized notification: when a row in a table marked
hashable is written, the write also carries the row's new content hash
and its two nearest ancestors in a Merkle-style containment hierarchy —
the minimum information a downstream consumer needs to incrementally
recompute an integrity tree without rescanning it from scratch.

`StorageObserver` itself declares three subscription methods —
`observe(table:events:)`, `observeBlobs()`, `observeDirtyChain()` — each
returning an `AsyncStream`, a Swift type for values that arrive over time.
Delivery is documented as at-least-once, and ordering is preserved within
one subscription but not promised across different tables. A protocol
extension supplies a default no-op stream for `observeDirtyChain()` so
older observer implementations, written before hash-on-write existed,
keep compiling without modification.

### StorageIntrospection.swift

This file provides an optional, separate capability protocol for
reporting backend health, plus the `StorageStats` value type that carries
the numbers. `StorageIntrospection` is deliberately not merged into
`Storage` itself — it is a distinct protocol so that adding it never
breaks an existing conformer, and a caller checks for the capability with
`storage as? StorageIntrospection` rather than assuming every backend has it.

`StorageStats` holds a superset of fields that no single backend fully
populates: page-level statistics such as `pageSize` and `walFrameCount`
are SQLite-only; buffer cache and transaction counters such as
`cacheHitRatio` and `deadlockCount` are PostgreSQL-only; `rowCount` and
`blobCount` are InMemory-only. A backend that cannot supply a given field
sets it to `nil` rather than fabricating a zero, so a caller can tell "not
measured" apart from "measured as zero." The file's doc comment includes a
field-by-field table cross-referencing exactly which backend fills which
field — the authoritative reference for anyone adding a new statistic.

### Transaction.swift

This file provides the transaction protocol and the three isolation
levels a caller may request: `readCommitted`, `repeatableRead`, and
`serializable`. `StorageTransaction` exposes the same three sub-stores
`Storage` does (`rowStore`, `blobStore`, `auditLog`) so that code written
inside a transaction block looks exactly like code written outside one —
the only difference is that every operation inside the block either all
commits together or all rolls back together. The file's own comment notes
that nested transactions and savepoints are out of scope for this version
of the design.

### StorageError.swift

This file provides the single closed error type every PersistenceKit
operation can throw. Being a closed `enum` (as opposed to an open
protocol) means every possible failure is enumerated in one place, so a
caller's `switch` over a `StorageError` can be exhaustive. The cases cover
backend availability, schema and migration failures, constraint and
uniqueness violations, connection-pool exhaustion, transaction conflicts,
type mismatches, and three especially important safety cases:
`corruptStoredValue` (a stored value could not be parsed back to its
declared type — thrown instead of silently substituting a fabricated
default, because a fabricated UUID or an epoch-zero date would be a
quiet data-identity lie), `invalidConfiguration` (an `EstateConfiguration`
requests something impossible on the current platform, such as an
Apple-only tagger on a non-Apple host), and `invalidIdentifier` (a
caller-supplied SQL column or table name contains characters outside the
safe set, which is the guard that keeps a dynamically-built query from
becoming a SQL-injection vector even though the name is already
double-quoted).

### Column.swift

This file provides `Column`, a `(table, name)` pair used throughout the
predicate and schema types to reference one column, and `ColumnType`, the
closed set of value kinds a column can hold: `uuid`, `bitmap`, `text`,
`timestamp`, `float`, `int`, `bool`, `blob`, `json`, `hlc`, and
`fingerprint`. `Column` conforms to `Comparable` (ordered first by table,
then by name) purely so test fixtures and other code that sorts columns
gets a stable, repeatable order.

### TypedValue.swift

This file provides `TypedValue`, the tagged union that carries every
value crossing the PersistenceKit boundary — the wire format of the whole
library. Every backend pattern-matches on the case and emits its own
native representation: SQLite might store a `.uuid` as a `TEXT` column,
PostgreSQL might store it as a native `UUID` column, but callers on both
sides always see the same Swift enum. The case set is deliberately
closed; the file's own comment says adding a new case is expected to
require updating every backend, and that this cost is the right price to
pay for true backend portability — a new value kind can never partially
work on only some backends. Two of the twelve cases, `.hlc` and
`.fingerprint`, exist specifically because PersistenceKit is forbidden
from reimplementing substrate math: the `HLC` and `Fingerprint256` types
themselves come from the `SubstrateTypes` dependency, and PersistenceKit
only needs a slot to carry them.

### Predicate.swift

This file provides `StoragePredicate`, the closed query-condition tree
every backend compiles to its own query language, plus `OrderClause` and
`OrderDirection` for sorting. The predicate cases fall into three
families: logical (`and`, `or`, `not`, `isTrue`, `isFalse`), comparison
(`eq`, `neq`, `lt`, `lte`, `gt`, `gte`, `isNull`, `isNotNull`, `in`,
`like`), and bitmap (`bitmaskAll`, `bitmaskAny`, `bitmaskNone`,
`bitwiseEq`, all restricted to integer-family columns). PersistenceKit
treats a predicate as opaque data except when compiling it — no backend
ever needs to special-case another backend's query shape, because the
tree is the same tree everywhere.

The two static helpers `all(_:)` and `any(_:)` build an `and`/`or` tree
from a list of predicates with useful short-circuiting: an empty list of
conditions to AND together collapses to `isTrue` (an always-true filter,
because "no restrictions" should match everything), an empty list to OR
together collapses to `isFalse`, and a list already containing a
contradictory trivial case (`isFalse` inside an `all`, `isTrue` inside an
`any`) collapses the whole expression immediately rather than building a
needlessly large tree.

### GeneratedColumn.swift

This file provides first-class computed columns: a column whose value is
derived from other columns in the same row by a small, structured integer
expression, rather than stored directly by a caller. `GeneratedColumn`
names the column, its result type, and its `GeneratedExpression`.
`GeneratedExpression` is a closed, recursive enum covering exactly the
bit-field algebra a bitmap-heavy schema needs: reading another column
(`.column`), a constant (`.literal`), bitwise AND/OR/XOR, left and right
shift by a fixed amount, and equality/inequality tests that evaluate to 1
or 0. The file's own comment explains why this is a structured expression
tree and not a raw SQL string: a SQL string generated column would push
backend-specific syntax into a schema declaration, and the in-memory
backend has no SQL engine to evaluate it against. A structured expression
has exactly one meaning, so SQLite and PostgreSQL render it to native
`GENERATED ALWAYS AS (...) STORED` DDL and the in-memory backend evaluates
it directly — three faithful realizations of one description, with no
escape hatch for one backend to interpret it differently from another.

`renderSQL()` turns an expression into a SQL fragment shared by SQLite and
PostgreSQL, because both use the same bitwise-operator syntax and
double-quoted identifiers; the one exception is bitwise XOR, which SQLite
lacks as an operator, so both backends instead render it as the
algebraically equivalent `(a | b) - (a & b)`. `evaluate(_:)` computes the
same result directly against an in-memory row dictionary, for the
in-memory backend. `integerValue(_:)` extracts an `Int64` from any
integer-family `TypedValue` (an `.int`, `.bitmap`, `.bool`, or `.hlc`),
returning zero for anything else or for an absent column, which is the
in-memory evaluator's sentinel for "no meaningful value here."

### EstateConfiguration.swift

This file provides `EstateConfiguration`, the one value that fully
describes how to open one estate's storage, and `BackendConfiguration`,
the closed choice of physical engine (`sqlite`, `postgresql`, `inMemory`)
with its connection parameters. `EstateConfiguration` also carries the
estate's encryption mode, cache configuration, and novel-token tagger
choice, each defaulted so that existing code that does not mention these
newer fields keeps compiling and keeps behaving exactly as it did before
they existed — a plaintext, uncached, HMM-tagged estate is what a caller
gets by simply not asking for anything else.

`queueSibling(filename:)` is a more specialized function: it derives a
second `EstateConfiguration` pointing at a companion database file that
sits beside the estate's own file — used, for example, so a work-queue
kit gets its own small database without needing separate key
distribution or a separate configuration path. For a SQLite estate at
`<dir>/<uuid>.sqlite`, asking for the sibling `"queue.sqlite"` produces
`<dir>/<uuid>.queue.sqlite` — the estate's own file stem is folded into
the sibling name so two different estates in the same directory can never
collide. For an in-memory estate, the sibling is also in-memory (both
ephemeral, which is the correct pairing for tests). For a PostgreSQL
estate, the function throws `StorageError.featureGated`: the design
deliberately defers a PostgreSQL queue sibling until later, and it fails
loudly rather than silently returning a half-working configuration.

The private helper `deriveQueueSiblingID(parentID:filename:)` computes the
sibling's estate ID deterministically — by XOR-folding the filename's
UTF-8 bytes into a 16-byte tag and then XOR-ing that tag with the parent
UUID's raw bytes — so the same parent estate and the same filename always
produce the same sibling ID, with no call to `UUID()` anywhere on this
path. Determinism here matters because every process that opens the same
estate must agree on the same sibling ID without any coordination.

### EstateCacheConfig.swift

This file provides `EstateCacheConfig`, the small value type that turns
row caching on or off for one estate and bounds how much a cache is
allowed to hold and how sensitive the content it holds may be.
`ceilingBytes` is clamped to be non-negative at construction, and
`sensitivityThreshold` is clamped to at most 2 — because sensitivity level
3, "Secret" in the ARIA adjective scale this kit shares with the rest of
the system, must never be cached under any configuration, and clamping at
construction enforces that invariant without relying on every caller to
remember the numeric boundary. `EstateCacheConfig.disabled` is the
zero-change default: an estate that never mentions caching gets exactly
the pre-caching behavior.

### EncryptionMode.swift

This file provides the three at-rest encryption modes and the
configuration value that carries one of them per estate.
`EncryptionMode.plaintext` stores content verbatim — encryption, if any,
happens only at a sharing boundary elsewhere in the system.
`.rowEncryption` (Mode 2) encrypts one column's content per row under a
key the row's own `keyID` column names. `.fullDatabase` (Mode 3) encrypts
the entire SQLite file, schema included, via SQLCipher's `PRAGMA key` at
the connection layer, which makes the per-row seam a no-op for that mode
because the whole file is already ciphertext on disk. A fourth mode,
database-plus-threshold encryption for a FedRAMP-tier deployment, is
deliberately absent from this enum — the file's comment stresses that the
capability does not exist in this build at all, so adding it later is a
conscious, reviewed act rather than an accidental unlock.

`EstateEncryptionConfig` bundles the mode with a key identifier and the
actual `SymmetricKey`. Its stored `key` property is `package`-scoped
rather than `public`: the SQLite backend, in a sibling module of the same
Swift package, needs it, but nothing outside the package should ever see
it. The convenience initializer `init(_ mode:)` mints a fresh, full-entropy
256-bit key and a UUID key identifier for either encrypting mode, and
mints neither for plaintext. `usesRowCrypto` is `true` only for Mode 2 —
Mode 3 protects everything at the file level, so the per-row seam has
nothing to do there. `fullDatabaseKeyHex` renders the whole-file key as
lowercase hex for the SQLCipher `PRAGMA key` statement and is documented
as something that must never be logged.

### RowCrypto.swift

This file provides the per-row AES-GCM-256 encryption used by
`EncryptionMode.rowEncryption`, plus the three write/read seam functions
that every backend calls at the same two moments: just before a write and
just after a read. Living in `PersistenceKit` core rather than in one
backend module is the point: both `PersistenceKitSQLite` and
`PersistenceKitPostgreSQL` call the exact same code, so the two backends
produce byte-compatible ciphertext envelopes without either one knowing
about the other.

`AeadProvider` is a small protocol seam — AEAD stands for Authenticated
Encryption with Associated Data, a class of algorithm that both encrypts
data and proves it was not tampered with. Any type conforming to
`AeadProvider` can replace the default algorithm without touching any
call site; the file names this as the extension point a future
FIPS-validated provider would use. `CryptoKitAeadProvider` is the default
implementation, backed by Apple's CryptoKit. It generates a fresh random
96-bit nonce on every single encrypt call — reusing a nonce under the same
key is the one mistake that breaks AES-GCM's security guarantee — and
returns the three parts concatenated as `[12-byte nonce][16-byte
tag][ciphertext]`, so a later decrypt is self-contained from the stored
bytes alone with nothing else to look up.

`encryptedForWrite(_:config:provider:)` is the write-side seam: for an
estate using row encryption, it encrypts the row's `content` column and
stamps a `keyID` column recording which key sealed it; for any other mode,
or for a row with no `content` column, it returns the values unchanged.
`decryptedForRead(_:config:provider:)` reverses this on read, but only
when the row's stored `keyID` matches the estate's own key identifier — a
mismatched `keyID` means the row was sealed under a key this estate does
not hold, so the function passes the row through as still-ciphertext
rather than attempting a decrypt that would only fail as an authentication
error. `assertContentKeyIDInvariant(_:table:config:)` is a final structural
guard: on an encrypting estate, any content-bearing row reaching a backend
write path with `.text` content and no `keyID` means the encryption seam
did not run somewhere upstream, and the function throws rather than let
that plaintext be persisted where a later read could never recover it.

### CachingRowStore.swift

This file provides `CachingRowStore`, a decorator that wraps any
`RowStore` and serves an in-memory hot tier of recently-read rows,
transparently — the file's own comment states the guarantee plainly:
every operation returns results identical to what the unwrapped backing
store would have returned, and the cache changes only latency, never
correctness.

The cache key is not just `(table, row key)`; it is that pair combined
with an `AsOfCoordinate`. A `.present` (current-state) read and an
`.asOf(hlc)` (past-state) read of the same row are cached as two entirely
separate entries, because a snapshot read against a pinned, immutable
past state can be cached forever — the GC pin mechanism guarantees that
data will not be vacuumed out from under it — while a present-state read
must be evicted the instant the row changes. `insert`, `upsert`,
`update`, and `delete` all evict the affected present-state entry (or, for
a batch predicate that does not identify one specific row, evict every
present-state entry for that table) after the underlying write succeeds.
An optional `parentChainProvider` closure, supplied by the calling kit,
lets a write also evict cached Merkle-aggregate entries for that row's
ancestors, so a cached rollup value never goes stale just because one leaf
changed underneath it.

Admission to the cache is sensitivity-gated. `isAdmissible(_:)` reads a
row's `provenance` bitmap column (when present) and decodes a six-bit
sensitivity field at bits 30–35, mapping the scale-gapped raw values 0,
16, 32, and 48 to the ordinals Normal, Elevated, Restricted, and Secret.
Secret content is rejected unconditionally, and anything above the
configured `sensitivityThreshold` is rejected too. An unrecognized bit
pattern, or a `provenance` value of the wrong `TypedValue` case, is also
rejected — the comments call this failing closed, meaning that any doubt
about a row's sensitivity keeps it out of the cache rather than risking
that sensitive content sits unencrypted in process memory longer than it
should. Eviction, when the estimated byte total exceeds the configured
ceiling, removes the least-recently-used entry first, tracked by a
monotonically increasing access counter rather than by wall-clock time.

All of this mutable state — the entry dictionary, the access counter, the
running byte total — lives inside a private `actor`, `CacheActor`. Because
every stored property of the actor is itself `Sendable`, wrapping the
state in an actor is what lets the outer `CachingRowStore` class, a
`final class` with no locks of its own, safely conform to `Sendable` under
Swift 6's strict concurrency checking.

### CacheInvalidator.swift

This file provides `CacheInvalidator`, the bridge between a
`StorageObserver`'s live change stream and a `CachingRowStore`'s
invalidation method — needed for the case where some other writer
bypasses the specific `CachingRowStore` instance entirely (for example,
a second process, or a raw connection opened for a migration) and
mutates the backing store directly. Without this bridge, the cache would
serve stale data forever after such a write, because it would never learn
the write happened.

One `CacheInvalidator` manages every table it is told to watch through a
single detached background task that fans out one child task per table,
using Swift's structured-concurrency `withTaskGroup`. `cancel()` cancels
the whole tree at once, and `deinit` calls `cancel()` automatically so a
caller who simply lets the invalidator go out of scope does not leak a
background task. The initializer's documentation is careful to flag one
narrow race: subscriptions start inside the background task, so a write
issued in the same instant as `init()` could in principle race ahead of
subscription registration; a caller with strict-ordering requirements is
advised to yield briefly (for example with `Task.sleep`) before relying on
the very first write being observed.

### HashingRowStore.swift

This file provides `HashingRowStore`, a decorator that intercepts writes
to any table marked hashable in the schema, computes a content hash for
the row, and emits a `DirtyChainEvent` so an integrity tree elsewhere in
the system can be kept current incrementally rather than rebuilt from
scratch on every write. PersistenceKit deliberately does not import a
hashing library itself; the actual hash function arrives as an injected
`ContentHashProvider` closure supplied by the calling kit (which does
import the hashing library), so this package stays free of that
dependency while still owning the mechanism that wires hashing into every
write path.

The trickiest part of this file is keeping the hash correct on partial
writes. An `insert` always carries the row's full column set, so hashing
the incoming `values` directly is correct. An `update`, however, typically
carries only the columns being changed — hashing just that partial
dictionary would produce a hash for a row that never actually existed in
storage. `augmentWithHashForKnownKey(table:rowKey:mergedValues:)` solves
this by pre-reading the row's current, full state, merging the incoming
changes on top of it, and hashing that merged result — the same approach
`upsert` uses when it resolves to an update against an existing row rather
than a fresh insert. Before hashing, the function strips any existing
`content_hash` column from the input, so the insert path and the
update-via-merge path always hash the same set of data columns and never
diverge just because one of them happened to already carry a stale hash
value.

`emitDirtyChain(table:rowKey:hashResult:)` delivers the resulting
`DirtyChainEvent` to an injected `ObserverRegistryRef` closure, which is
`nil` for backends without observer support — the hash is still computed
and stored, but the notification step is simply skipped.

### ErasureLedger.swift

This file provides the append-only record of what has been erased,
without ever storing the erased content itself. A "drawer," in this
context, is one unit of stored content (the term comes from the estate's
own filing metaphor, not from this package). `ErasureLedgerEntry` pairs a
`drawerId` with the `HLC` at which it was erased. The `erasure_ledger`
table this module declares is marked append-only in its
`TableDeclaration` — every backend enforces this at the storage layer, so
even a caller that somehow bypasses the ledger's own API cannot mutate or
remove an entry once written, which is exactly the tamper-evidence
property an erasure record needs.

`recordErasure(rowStore:drawerId:erasedHlc:)` inserts one entry and
throws `StorageError.duplicateKey` if that drawer was already erased —
each drawer is erased exactly once. `isErased(rowStore:drawerId:)` is the
fast point-lookup used on every read that might need to hide erased
content; `lookupErasure(rowStore:drawerId:)` returns the full entry when a
caller needs the erasure timestamp itself, not just a yes/no answer.

### ErasureOverlay.swift

This file provides the two-phase, fail-closed filter that actually hides
erased content from a query result, built on top of the ledger this
module does not itself define. `ErasureOverlayConfig` is supplied by a
higher kit and carries two pieces of entity-specific knowledge
PersistenceKit does not have on its own: `extractErasureId`, a closure
that pulls the erasure-ledger key out of a result row (or returns `nil`
for a row type that is not subject to erasure at all), and
`contentColumns`, the list of column names to null out when a row turns
out to be erased.

`ErasureOverlay.apply(rows:config:rowStore:)` runs the two phases the file
describes at the top: phase one is an ordinary query that already
happened before this function is called; phase two walks the returned
rows one at a time, checks each one's erasure ID against the ledger, and
nulls its content columns if erased. The important design decision is
what happens when the ledger check itself throws an error instead of
returning a clean yes-or-no answer: the row is dropped from the result
entirely. This is the fail-closed half of "two-phase fail-closed" — an
uncertain answer about whether a row was erased is treated the same as
"it was," because showing content that should have been erased is a worse
failure than temporarily hiding content that was not.

### GCPin.swift

This file provides the query that tells a garbage-collection pass which
rows it must not touch yet. GC, short for garbage collection, is any
maintenance pass that reclaims space by deleting rows that are no longer
needed — for example, old versions of a row that a new version has
superseded. The problem this file solves: a snapshot taken earlier (via
`SnapshotRegistry`) might still need to read an "old" row that the GC pass
would otherwise consider safe to delete.

`GCPin.minimumRetainableHlc(rowStore:)` answers this by finding the
smallest HLC across every currently-registered snapshot — the oldest live
snapshot's timestamp is the pin. Any row whose HLC is at or after that
pin must survive a GC pass; anything strictly older is safe to reclaim.
When no snapshots exist at all, the function returns `nil`, meaning
nothing is pinned and every row is fair game for GC. `isPinned(rowStore:
rowHlc:)` is the convenience per-row check built on top of the same query.

### SnapshotRegistry.swift

This file provides the durable record of named point-in-time snapshots
and the cryptographic attestations that accompany them. A snapshot, in
this design, is not a copy of any data — it is a registry row recording
one HLC (the moment the snapshot was taken) plus one or more attestation
rows recording what a Merkle root looked like at that moment for a given
subject. PersistenceKit itself knows nothing about what a "subject" is —
the `subjectKind` and `subjectId` strings are supplied by whichever
higher-level kit is taking the snapshot.

`SnapshotId` is a UUID-backed opaque identifier, minted fresh by
`SnapshotId.mint()`. `SnapshotRegistryOps.createSnapshot(rowStore:hlc:
label:createdAt:attestations:)` mints an ID, inserts one registry row, and
inserts one attestation row per subject passed in, returning the
completed `SnapshotRecord` to the caller. `listSnapshots(rowStore:)` walks
every registered snapshot in HLC order; `deleteSnapshot(rowStore:
snapshotId:)` removes a snapshot's attestation rows before removing its
registry row (child rows first, so no orphaned attestation can ever
reference a deleted registry entry) and reports whether anything existed
to delete. `attestations(rowStore:snapshotId:)` reads back every
attestation for one snapshot, ordered by subject for deterministic
output. The remaining private helpers handle the mechanical work of
turning a `StorageRow` into a typed `SnapshotRecord` or
`SnapshotAttestation` and back.

### NoOpObserver.swift

This file provides `NoOpObserver`, a trivial `StorageObserver`
conformance whose three subscription methods each return a stream that
finishes immediately, delivering nothing. It exists as a placeholder for
any backend or test path that has no real change-notification mechanism
yet but still needs to satisfy the `Storage` protocol's requirement for
an `observer` property — the PostgreSQL backend, for instance, uses this
type directly rather than implementing a live PostgreSQL notification
channel.

### NovelTokenTaggerChoice.swift

This file provides the estate-creation-time choice of which tagger
classifies a "novel token" — a word not found in a language model's
existing vocabulary — when an estate needs to do that classification work
somewhere in its processing pipeline. The two cases are `.hmm`, a
deterministic Hidden Markov Model tagger that produces the exact same
output on every platform and is therefore safe to use across a federation
of devices, and `.nlTagger`, Apple's `NaturalLanguage` framework tagger,
which can be more accurate on Apple hardware but is not available outside
Apple platforms and is not guaranteed to produce the same output across
different OS versions.

This choice is fixed permanently at estate creation in this version of
the design; changing it later, with re-tagging of existing content, is
explicitly deferred to a future version. The file's extensive comments
spell out the practical consequence: an estate created with `.nlTagger`
cannot safely federate (share and compare memories) with an
`.hmm`-tagged estate without re-tagging all of its content first, because
the two taggers can disagree on the same word. Enforcing that constraint
automatically is also deferred; for now, a caller who selects `.nlTagger`
is responsible for not mixing that estate into a federation with
incompatible estates. `NovelTokenTaggerChoice.default` is `.hmm` — the
safe, federation-compatible baseline every estate gets unless a caller
explicitly opts into the Apple-only alternative. An identically-named,
independently-defined enum exists in `LatticeLib`, in a different package;
the two packages deliberately do not share this type by importing one
from the other, so that neither package depends on the other.

### PersistenceKitTelemetry.swift

This file provides `reportStorageStats(_:estateID:now:)`, the function
that turns a `StorageIntrospection.stats(now:)` snapshot into a stream of
named metrics through `IntellectusLib`, a lightweight, zero-dependency
telemetry library. The function's entire behavior is gated on
`Intellectus.isEnabled`, which defaults to `false`. When telemetry is
disabled — the default for essentially every deployment — the function
returns after a single atomic boolean load, without ever calling
`stats(now:)` and without building a single metric object; the file's
comment quantifies this cost at roughly one nanosecond, making the
disabled path effectively free.

When telemetry is enabled, the function emits one metric per non-`nil`
field of the captured `StorageStats`, each under the `persistence.db.*`
namespace and each tagged with the emitting kit's name and the estate's
ID, so a monitoring backend can filter and group by estate. Because
`StorageStats` fields are `nil` exactly where a backend cannot supply
them, the emitted metric set naturally differs by backend without this
function needing to know anything about which backend produced the
snapshot — an in-memory estate simply never emits a `wal_frames` metric,
because that field was never populated in the first place. Every
timestamp passed to `Intellectus.report(_:)` comes from the caller-supplied
`now` parameter, never from a fresh `Date()` call inside this function,
which is a deliberate determinism rule followed throughout the whole
package: any code that might run identically twice must never read the
wall clock itself.

## Target: PersistenceKitInMemory

### InMemoryStorage.swift

This file provides `InMemoryStorage`, the backend used for tests and for
any storage that should live only as long as the current process, plus
the `InMemoryStateActor` that owns every piece of mutable state behind
Swift's actor isolation. There is no file on disk and no network
connection; every table is a plain Swift dictionary held in memory.

The most carefully documented piece of this file is how `transaction(
isolation:_:)` implements rollback. Rather than running the caller's
block against a private, detached copy of the state and only replacing
the live state on success, it runs the block directly against the live
actor state and takes a snapshot copy only for the error path. The file's
own comment explains why the seemingly simpler "copy, mutate, replace on
success" approach is actually wrong: if any other write reaches the live
actor state between the moment the snapshot was taken and the moment a
successful transaction would replace the whole state with its
snapshot-derived copy, that concurrent write would be silently erased by
the replacement. The comment traces this exact bug to a real incident — a
burst of concurrent inserts racing a transaction lost five to ten percent
of queued work. Running against live state avoids the problem entirely,
at the cost of needing an explicit rollback-to-snapshot path when the
block throws.

Change notifications during a transaction are buffered rather than
delivered immediately, for a related reason: if a subscriber were notified
of a write that the transaction later rolled back, it would have observed
something that, from the estate's point of view, never actually happened.
`beginNotificationBuffering()` starts collecting notifications instead of
delivering them; `commitNotifications()` flushes the buffer to observers
only after the block has returned successfully; the rollback path
discards the buffer instead. `insertRow`, `upsertRow`, `updateRows`, and
`deleteRows` are the four mutating operations, each of which computes any
declared `GeneratedColumn` values via the static
`materializeGenerated(_:_:)` helper before storing the row, matching what
SQLite and PostgreSQL compute natively so a query against any backend
returns the same generated values. `queryRows(...)` applies the predicate
and ordering against full rows first and only narrows to the requested
column projection at the very end, so a projected query can still filter
or sort on a column it does not actually return — matching SQLite's
`SELECT`-list-versus-`WHERE`/`ORDER BY` semantics exactly.

`InMemoryStorage` also conforms to `StorageIntrospection`, reporting an
approximate byte size (a flat per-row estimate plus exact blob byte
counts — described in the file as a rough signal, not a precise
allocator measurement), a live row and blob count, and a monotonically
increasing rollback counter.

### InMemoryRowStore.swift

This file provides the thin `RowStore` conformance that simply forwards
every call to the shared `InMemoryStateActor`. Its one piece of design
worth noting is the column-projection overload: because the in-memory
backend already holds the full row in memory, projecting away unrequested
columns saves no actual transfer cost the way it does for SQLite — the
comment is explicit that the point of implementing it anyway is
consistency, so a `StorageRow` returned from a projected in-memory query
is byte-identical in shape to the same projected query against SQLite,
and code under test cannot accidentally depend on a column being present
just because the in-memory backend happened to still have it in memory.

### InMemoryBlobStore.swift

This file provides the `BlobStore` conformance for the in-memory backend
— five one-line forwarding methods to the shared state actor's blob
dictionary. There is nothing backend-specific to explain here beyond what
`BlobStore.swift` already documents; this file exists purely to satisfy
the protocol against the actor's storage.

### InMemoryAuditLog.swift

This file provides the `AuditLog` conformance for the in-memory backend,
again a thin forwarding layer to the shared state actor, which itself
de-duplicates on `(eventID, hlc)` exactly as the protocol's idempotence
contract requires.

### InMemoryObserver.swift

This file provides `ObserverRegistry`, the subscription bookkeeping
shared by every in-memory row store, blob store, and audit log instance
backed by the same state actor, and `InMemoryObserver`, the thin
`StorageObserver` conformance built on top of it. The registry
deliberately does not use actor isolation for its subscriber lists;
instead it uses a plain `NSLock`. The file's comment explains the
trade-off precisely: registering a subscription must be synchronous, with
the stream already recording it before `observe()` returns to the caller,
so that a change notified the very next line of code can never race ahead
of the subscription being recorded. An `actor`-based registry would force
`register` to be an `async` function, reopening exactly that race. This
mirrors the equivalent Rust observer hub, which registers synchronously
for the same reason.

`notify(_:)` snapshots the list of matching subscribers under the lock
and then yields to each of them entirely outside the lock, so that a
subscription's own termination handler — which also needs the lock to
remove itself from the list — can never deadlock against an in-flight
notification. Row, blob, and dirty-chain subscriptions are tracked in
three separate dictionaries but follow the same synchronous-register,
lock-scoped-snapshot, unlocked-delivery pattern throughout.

### PredicateEvaluator.swift

This file provides the in-memory interpreter for `StoragePredicate` —
the only backend that evaluates the predicate tree directly against
Swift values in memory rather than compiling it to a query string first.
`PredicateEvaluator.evaluate(_:against:)` walks the tree recursively,
handling every case from `Predicate.swift` by reading the named column
out of a `[String: TypedValue]` row (treating a missing column the same
as an explicit `.null`) and applying the matching comparison, bitmask
test, or `LIKE`-style text match. `likeMatch(_:pattern:)` translates a
SQL `LIKE` pattern (`%` for any run of characters, `_` for exactly one)
into an `NSRegularExpression`, so the in-memory backend matches the same
patterns SQL's `LIKE` operator would.

`TypedValueComparator.compare(_:_:)`, in the same file, is the shared
ordering function used both by predicate comparisons and by `ORDER BY`
sorting: `nil` (that is, `.null`) sorts before every other value, and two
values of the same case compare by their underlying Swift value, with
`.hlc` compared by its packed integer form (`HLC.packed`, defined at the
bottom of `InMemoryStorage.swift`) to get a single total order over an
HLC's three component fields.

## Target: PersistenceKitSQLite

### SQLiteStorage.swift

This file provides `SQLiteStorage`, the `Storage` conformance for the
on-device backend, and the much larger `SQLiteBackend` actor that does
essentially all of the real work — schema migration, every row and blob
operation, audit logging, and health introspection — serialized through
one actor per estate, matching the file's opening comment that a SQLite
estate gets exactly one connection, with SQLite's own WAL (write-ahead
log) mode handling concurrent readers safely underneath.

Schema migration walks a schema's declared `Migration` list, running each
pending migration inside its own `BEGIN IMMEDIATE`/`COMMIT` pair and
rolling back and throwing `StorageError.migrationFailed` if any operation
in that migration fails. `applyMigrations(_:)` is written to be safe to
call even on a completely fresh SQLite file that was never opened through
`openSchema(_:)` first — a documented real scenario, since at least one
caller invokes `migrate(to:)` directly — by re-running the idempotent
table- and migrations-table-creation steps before checking what actually
needs to migrate.

Every row-mutating method — `insertRow`, `upsertRow`, `updateRows`,
`deleteRows`, `queryRows` — begins by calling `validateSQLIdentifier` on
every table and column name it is about to interpolate into a SQL string.
The file's comments tie this directly to a named security fix
(SECFIX-WS2-PK F9): double-quoting a SQL identifier is not sufficient
protection if the identifier itself can contain a double-quote character,
because that character can escape the delimiter and alter the query, so
every name is checked against a strict allow-list before it is ever
placed into a SQL string. `insertRow` and `queryRows` are also where the
at-rest row-encryption seam actually runs: `insertRow` calls
`encryptedForWrite` before binding values, and `queryRows` calls
`decryptedForRead` after reading them back, both of which are no-ops on a
plaintext or whole-database-encrypted estate.

`readColumn(stmt:index:schema:columnName:table:)` is the file's most
carefully reasoned function: it decides, column by column, whether a raw
SQLite value should be trusted as-is or treated as corrupt. The
distinction the comment draws is between a type-tolerant decode (a valid
value stored under SQLite's flexible column affinity, which is passed
through as-is) and a genuine parse failure on a column whose declared
type says it should parse (an unparseable UUID or timestamp string),
which throws `StorageError.corruptStoredValue` rather than ever
fabricating a random UUID or an epoch-zero date in its place.
`queryRowsSkipCorrupt(...)` reuses the same column-reading logic but
catches exactly that error per row, logs it, counts it, and continues to
the next row — the cursor-level implementation `RowStore`'s protocol
default can only approximate by discarding an entire query's results.

`storageStats(now:)` implements `StorageIntrospection` for SQLite by
reading a handful of read-only `PRAGMA` statements (`page_size`,
`page_count`, `freelist_count`) and deriving the WAL frame count directly
from the `-wal` sidecar file's size on disk rather than calling `PRAGMA
wal_checkpoint`, because that particular PRAGMA can fail with a locked
error if a concurrent operation is in flight, even from inside the same
serializing actor.

### SQLiteConnection.swift

This file provides `SQLiteConnection`, a thin Swift wrapper around the C
`sqlite3` API, `SQLiteStatement`, the prepared-statement wrapper used for
every parameterized query, and `ISO8601`, the shared timestamp
formatting and parsing utility used everywhere a `.timestamp` `TypedValue`
crosses the SQLite boundary.

Opening a connection does several things in a specific, safety-relevant
order. First, it checks whether the target path is a symbolic link and
refuses to open it if so — the comment names this CAND-052, a defense
against a pre-planted symlink that could otherwise redirect SQLite's
writes to an arbitrary file elsewhere on disk, and notes that the check
uses `lstat` semantics (via `resourceValues(forKeys:)`) so it identifies
the symlink itself rather than following it to whatever it points at.
Second, if the estate uses whole-database encryption, it issues `PRAGMA
key` with the estate's hex-encoded key before any other statement runs —
this must be the very first statement, because SQLCipher cannot even read
the schema on page one of the file without the correct key already
applied. Third, it applies Apple's file-level Data Protection, a
best-effort step that layers OS-level at-rest protection on top of
SQLCipher's own encryption. Finally, it sets the durability pragmas: WAL
journal mode, `NORMAL` synchronous durability, a WAL auto-checkpoint
threshold, a busy timeout, and foreign-key enforcement.

`ISO8601` deserves its own explanation because two very different
performance and correctness stories live in it. `string(from:)` first
clamps an out-of-range `Date` (for example one accidentally derived from
milliseconds mistaken for seconds) into the range `ISO8601DateFormatter`
can actually parse back — years 0001 through 9999 — because writing an
unparseable string like `+59009-...` would silently corrupt any future
read of that row; a clamp with a logged warning is judged better than a
value nothing can ever read back correctly. `date(from:)` tries a hand-
written, allocation-free parser, `fastParseCanonicalUTC(_:)`, before
falling back to the much slower ICU-backed `ISO8601DateFormatter`. The
comment explains why the fast path exists at all: a stack-sampling
profile during a large import showed the formatter's `date(from:)`
consuming roughly eighty percent of total CPU time, because a Merkle
rollup re-decodes every row's timestamp on every insert, making the parse
cost scale quadratically with import size. The fast parser recognizes
only the exact canonical shape this kit itself writes and returns `nil`
for anything even slightly different (a numeric time-zone offset,
lowercase letters, trailing garbage), which sends control back to the
slow, fully general formatters — so correctness never regresses, and the
fast path is verified byte-for-byte against the formatters in a dedicated
test suite.

### SQLiteStores.swift

This file provides `SQLiteRowStore`, `SQLiteBlobStore`, `SQLiteAuditLog`,
and `SQLiteTransaction` — four small wrapper types that each forward to
the shared `SQLiteBackend` actor, giving the actor's internal methods
their public `RowStore`/`BlobStore`/`AuditLog`/`StorageTransaction`
faces. `SQLiteRowStore.querySkipCorrupt(...)` is the one method here with
real logic of its own: rather than inheriting `RowStore`'s protocol-
extension default (which can only discard an entire failed query), it
calls `SQLiteBackend.queryRowsSkipCorrupt(...)` directly, so a corrupt
row found partway through a large corpus scan is skipped and logged
individually rather than aborting everything already read.

### SQLiteSchema.swift

This file provides `SQLiteSchema`, the enum of pure functions that
translate a `SchemaDeclaration` into SQLite's native DDL (data definition
language — the SQL used to create and alter tables, as opposed to the SQL
used to read and write rows). `nativeType(_:)` maps every
`ColumnType` case to its SQLite storage class — most map directly, but
`.uuid` and `.timestamp` both map to `TEXT` because SQLite has no native
types for either, `.hlc` maps to `INTEGER` because it is stored as a
packed 64-bit value, and `.fingerprint` maps to `BLOB` because it is
stored as raw bytes. `createTable(_:)` assembles one `CREATE TABLE IF NOT
EXISTS` statement covering ordinary columns, generated columns (always
rendered `STORED`, matching PostgreSQL's lack of a `VIRTUAL` form),
primary key, and unique constraints. `appendOnlyTriggers(_:)` emits a
`BEFORE UPDATE`/`BEFORE DELETE` trigger pair for any table declared
append-only, each one aborting the statement outright with `RAISE(ABORT,
...)` — the actual enforcement mechanism behind, for example, the erasure
ledger's append-only guarantee.

This file also owns the three internal bookkeeping tables every SQLite
estate carries regardless of what schema a caller declares:
`_storagekit_migrations` (tracks each kit's applied schema version),
`_storagekit_audit` (the single source of truth for every audit event,
with full-precision HLC columns alongside the packed integer form — the
packed form loses a bit of the physical-time field for dates far enough
in the future, so the full-precision columns exist specifically so a
cold rebuild of an estate's last-known HLC never silently disagrees with
what a snapshot originally recorded), and `_storagekit_blobs` (the flat
key/bytes table backing `SQLiteBlobStore`).

### SQLitePredicateCompiler.swift

This file provides the translation from a `StoragePredicate` tree into a
parameterized SQLite `WHERE` clause plus its ordered list of bound
values. `compile(_:)` is the public entry point; the private recursive
`render(_:bindings:)` walks the tree case by case, validating every
column name it touches with `validateSQLIdentifier` before it is
interpolated, and appending every comparison value to the bindings array
rather than ever interpolating a value directly — values are always safe
because SQLite's own parameter-binding mechanism handles them, but column
and table identifiers cannot be bound as parameters in SQL, which is
exactly why they need the separate identifier-validation guard instead.

### SQLiteIdentifierValidator.swift

This file provides `validateSQLIdentifier(_:)`, the single free function
that every write path and the predicate compiler in this module call
before interpolating any caller-supplied name into a SQL string. The rule
is simple and strict: the first character must be a letter or an
underscore, and every following character must be a letter, a digit, or
an underscore — the safe subset of SQLite identifier syntax. The file's
own comment states the reason this exists as one shared function rather
than several private copies: a name containing a double-quote character
can escape the double-quote delimiter SQL uses around identifiers and
change what the query actually does, so double-quoting alone is not a
sufficient defense, and having one seam means the defense cannot silently
drift out of sync between the several call sites that need it.

### SQLiteObserver.swift

This file provides `SQLiteObserverRegistry`, an `actor` holding the
subscriber lists for both row-change and blob-change notifications on one
SQLite estate, and `SQLiteObserver`, the public `StorageObserver`
conformance built on top of it. The file's own comment explains a real
limitation of SQLite's native `sqlite3_update_hook`: it fires for every
table, including the internal blob table, but it only reports which
operation happened on which table and row ID — it never carries the
actual column values. Because a blob-change notification needs to carry
the bytes that were written, blob notifications cannot be reconstructed
from the hook at all; instead, `SQLiteBackend`'s `putBlob`/`deleteBlob`
methods call this registry's `notifyBlob(_:)` directly, at the exact
point where the bytes are already in hand.

### KeychainKeyStore.swift

This file provides `KeychainKeyStore`, the Apple-platform source of the
whole-database (Mode 3) encryption key — the Apple counterpart to the
Rust port's per-estate key file on disk. One 256-bit key is stored per
estate in the system Keychain, under an account name derived
deterministically from the estate's own file path (`estateAccount(for:)`
standardizes the path and hashes it with SHA-256), so any process that is
told the same estate's file path — the main app and a managed
background server it spawns, in particular — computes the same account
name and therefore loads the same key without any separate key-
distribution step. `loadOrCreateKey()` reads an existing key if one is
present, or generates a fresh cryptographically random key and stores it
if not, handling the race where two callers try to create the key
simultaneously by having the loser simply re-read what the winner wrote.
`deleteKey()` disposes of an estate's key permanently when the estate
itself is deleted, and is documented as idempotent — calling it on an
estate whose key is already gone is a success, not an error. The stored
key's Keychain accessibility level, `afterFirstUnlockThisDeviceOnly`, is
chosen specifically so a background process can still read the key after
the very first unlock following a device restart, without the key ever
leaving the device or syncing to iCloud.

## Target: PersistenceKitPostgreSQL

### PostgreSQLStorage.swift

This file provides `PostgreSQLStorage`, the `Storage` conformance for the
server backend, and the `PostgreSQLBackend` actor that holds the schema
declaration and drives migrations, transactions, and health
introspection against a connection pool. The most distinctive design
decision here is estate isolation: rather than one PostgreSQL database
per estate, every estate gets its own PostgreSQL schema — a namespace
within one shared database — named `pk_<estate UUID with dashes
removed>`. Every pooled connection for that estate pins its
`search_path` to that schema (keeping `public` on the path too, so any
shared extension still resolves), which is the PostgreSQL analogue of
SQLite's one-file-per-estate model: many estates can share one database
server without their tables ever colliding.

Schema-version bookkeeping uses a simple key-value table,
`_storagekit_meta`, rather than a dedicated migrations table like
SQLite's: a per-kit version is stored under the composite key
`"schema_version:<kitID>"`, and a running-maximum global version is kept
under the plain key `"schema_version"` so the no-argument
`currentSchemaVersion()` still returns something meaningful even when
several kits share one estate. `storageStats(now:)` implements
`StorageIntrospection` for PostgreSQL by querying `pg_database_size`,
`pg_stat_database` (for buffer-cache hit ratio and transaction counters),
and `pg_locks` joined against `pg_database` (for lock contention) — three
different system views, each documented in the file with the exact
formula used to turn its raw counters into the `StorageStats` fields.

### PostgreSQLPool.swift

This file provides `PostgreSQLPool`, a fixed-size connection pool built as
an `actor` so its bookkeeping — available connections, in-use count,
waiters — never needs a separate lock. `acquire()` returns an idle
connection immediately if one exists, opens a brand-new one if the pool
has not yet reached its configured size, or otherwise suspends the caller
on a `CheckedContinuation` until either a connection frees up or a timeout
elapses, at which point the waiter is resumed with
`StorageError.poolExhausted`. Every freshly-opened connection is
immediately pinned to the estate's dedicated PostgreSQL schema by issuing
`CREATE SCHEMA IF NOT EXISTS` followed by `SET search_path`, and the
connection is closed rather than kept if that two-statement setup fails
partway through, so a half-configured connection is never handed back to
a caller.

`parseTLSMode(host:)` resolves the pool's TLS behavior from the
`ARIA_MCP_POSTGRES_TLS` environment variable, defaulting to `prefer` (try
TLS, fall back to plaintext if the server does not offer it) whenever the
variable is absent or holds an unrecognized value — the comment is
explicit that even a loopback connection defaults to `prefer` rather than
`disable`, so a caller who genuinely wants plaintext on loopback has to
say so explicitly rather than getting it as an accidental default.

### PostgreSQLConnection.swift

This file provides free functions that bridge between PersistenceKit's
`TypedValue` and the `postgres-nio` client library's own binding and
decoding types. `executeSimple(_:logger:)` and
`executeParameterized(_:bindings:logger:)` are small `PostgresConnection`
extension methods that wrap every underlying `postgres-nio` error in
`StorageError.backendError`, so a caller never has to catch a
`postgres-nio`-specific error type directly. `makeBindings(_:)` converts
an array of `TypedValue` into the positional `$1, $2, ...` bindings
PostgreSQL's parameterized-query protocol expects, handling each
`TypedValue` case's specific wire representation — a `.fingerprint`, for
example, is serialized as 32 raw bytes in a fixed block order so it
round-trips exactly.

`decodeRow(_:columns:)` and the private `decodeCell(_:type:)` do the
reverse: given a returned `PostgresRow` and the caller's expected column
types, they decode each named cell into the matching `TypedValue` case,
falling back to `.null` if a cell fails to decode as its declared type
rather than throwing — a deliberately more permissive stance than the
SQLite backend's `corruptStoredValue` throw, reflecting that PostgreSQL's
own wire protocol already enforces column types at a lower level than
SQLite's flexible column affinity does.

### PostgreSQLPredicateCompiler.swift

This file provides the PostgreSQL analogue of `SQLitePredicateCompiler`:
the same `StoragePredicate` tree compiled to PostgreSQL's own SQL dialect,
using `$1, $2, ...` positional parameters instead of SQLite's `?`
placeholders. The case-by-case translation logic is otherwise the same
shape as the SQLite compiler — every column name is validated with
`validatePSQLIdentifier` before being interpolated, and every comparison
value becomes a bound parameter rather than inline text. One PostgreSQL-
specific detail: the bitmask predicate cases (`bitmaskAll`, `bitwiseEq`)
have to reference two already-appended bindings by their final numeric
position (`bindings.count - 1` and `bindings.count`), because PostgreSQL's
positional parameters are numbered by the order they were bound, unlike
SQLite's simple `?` placeholders which do not carry a number at all.

### PostgreSQLSchema.swift

This file provides `PostgreSQLSchemaEmitter`, the PostgreSQL analogue of
`SQLiteSchema`: pure functions translating a `SchemaDeclaration` into
PostgreSQL DDL. `typeSQL(_:)` maps each `ColumnType` to its PostgreSQL
native type — notably `.uuid` maps to a real `UUID` column (unlike
SQLite's `TEXT` fallback), `.timestamp` maps to `TIMESTAMPTZ`, and `.json`
maps to `JSONB`, PostgreSQL's indexable binary JSON type. Because
PostgreSQL has no `CREATE TRIGGER IF NOT EXISTS`, `appendOnlyTriggerStatements(_:)`
achieves the same idempotence SQLite gets for free by first dropping any
existing trigger of the same name and then creating it fresh, backed by
one shared `appendOnlyFunctionSQL` trigger function (created with `CREATE
OR REPLACE`, so re-running schema setup never fails) that raises an
exception naming the offending table via PostgreSQL's `TG_TABLE_NAME`
variable — one function serving every append-only table in the schema,
rather than one function generated per table.

### PostgreSQLStores.swift

This file provides `PostgreSQLRowStore`, `PostgreSQLBlobStore`, and
`PostgreSQLAuditLog` — the concrete `RowStore`, `BlobStore`, and
`AuditLog` conformances for the PostgreSQL backend, each able to run
either against a pooled connection acquired for a single call or against
a `PostgreSQLTransactionContext`'s already-open connection when called
from inside a transaction block. Every write method here validates SQL
identifiers with `validatePSQLIdentifier`, exactly as the SQLite backend
does, before assembling its SQL string.

`PostgreSQLRowStore.query(...)` reads the schema's declared column list
for the target table (rather than issuing a bare `SELECT *`) specifically
so a generated column's PostgreSQL-computed value is decoded with the
correct `ColumnType`, and it runs every returned row's values through
`decryptedForRead` before wrapping them in a `StorageRow`. `update(...)`
and `delete(...)` both compile their predicate starting at parameter
index one past the last `SET`-clause binding, using the private
`renderPredicate(_:startIndex:bindings:)` helper, which compiles the
predicate normally and then renumbers every `$N` placeholder in reverse
order — high numbers first — specifically to avoid `$10` being
accidentally rewritten by a naive forward replacement of `$1`.
`PostgreSQLBlobStore` and `PostgreSQLAuditLog` each lazily create their
own backing table (`_storagekit_blobs`, `_storagekit_audit`) on first use
via `CREATE TABLE IF NOT EXISTS`, rather than requiring the caller's
schema declaration to know about them — the same design PersistenceKit
uses for SQLite's internal tables. `decodeAuditEvent(_:)` decodes the
audit row's required fields strictly (a decode failure there propagates
as a thrown error) while treating optional before-state fields
permissively (`try?`, because `NULL` is the expected, valid value for "no
prior state").

### PostgreSQLIdentifierValidator.swift

This file provides `validatePSQLIdentifier(_:)`, the PostgreSQL-module
analogue of `SQLiteIdentifierValidator.swift`'s `validateSQLIdentifier`,
enforcing the exact same rule — first character a letter or underscore,
every later character a letter, digit, or underscore. The file's own
comment names this as one of three independent seams implementing the
identical rule: this one, the SQLite module's, and the Rust port's
`validate_sql_identifier` — three copies by necessity, since Swift
extensions cannot share a free function across two separate targets
without a shared dependency neither module otherwise needs, but each
copy is the single seam within its own module, so within a module the
rule cannot silently drift.

## Target: PersistenceKitReplication

### ReplicationTypes.swift

This file provides the two public types every other file in this module
builds on. `ReplicationCursor` is the opaque watermark a caller stores
and passes back into a later incremental sync: it carries the highest HLC
seen across every copied row and audit event, plus counts of rows,
audit events, and blobs written during that run. `ReplicationError` is
the closed error type specific to replication — `schemaMismatch` (source
and destination disagree on schema version or kit ID; replication
deliberately refuses to auto-migrate either side) and `storageFailure`
(an underlying `StorageError` surfaced during the copy, re-wrapped as a
plain string so `ReplicationError` itself can stay `Equatable`, which a
raw `Error` cannot generally guarantee).

### StorageReplicator.swift

This file provides `StorageReplicator`, the full-snapshot replication
primitive: `replicate(from:to:schema:)` and its two direction-named
conveniences, `flush(from:into:schema:)` (in-memory into durable storage)
and `hydrate(into:from:schema:)` (durable storage into a fresh in-memory
instance). "Full snapshot" means exactly what it says — every run copies
every row in every schema-declared table, every audit event, and every
blob, regardless of what changed since the last run. The function is
still idempotent, though: every row upsert uses the table's own primary
key as its conflict column, not the random UUID a fresh `RowHandle` would
carry, so running the same flush twice against an unchanged source
updates existing destination rows in place rather than ever inserting a
second copy.

The implementation runs in three ordered steps. Step one is a schema
gate: source and destination must report the exact same per-kit schema
version, or the function throws `ReplicationError.schemaMismatch`
immediately rather than attempting any copy. Step two reads the entire
source — every table's rows (with any generated columns filtered out of
each row, because writing a value into a column the destination computes
itself would be rejected by both SQL backends), every audit event, and
every blob — into one `Sendable` in-memory payload, before the
destination transaction ever opens; the file's comment explains this
ordering exists so the destination is never left holding a long-lived
serializable transaction open while potentially slow source I/O runs
underneath it. Step three writes that entire payload into the destination
inside one serializable transaction, so a crash or a thrown error partway
through leaves the destination exactly as it was before the flush began.
A blob-deletion pass at the end of step three removes any destination
blob key absent from the source snapshot, closing a gap the file
documents by its fix identifier (SECFIX-WS2-PK F5): without it, a blob
deleted at the source would linger forever in the destination after every
future full-snapshot flush, because an additive-only copy never notices
an absence.

### IncrementalReplicationSession.swift

This file provides `IncrementalReplicationSession`, the observer-driven
alternative to a full snapshot: instead of copying everything on every
run, it watches a source's live change stream and, on demand, syncs only
the rows and blobs that actually changed since the last sync. The file's
opening comment lays out the design choice explicitly: rather than a
durable dirty-row table written on every observed change (which would tie
this backend-agnostic module to one particular storage schema), the
session accumulates dirty identifiers purely in memory, inside two actors
— `DirtySet` for rows and `BlobDirtySet` for blobs — and re-reads each
dirty row's current state from the source at sync time rather than
trusting whatever the original change notification said, because the row
may have changed again since it was first marked dirty. If the process
restarts, the in-memory dirty-set is lost; the documented recovery is
simply to fall back to a full snapshot, which is always a safe substitute
for a lost incremental history.

`DirtyKey` identifies one dirty row by its table name and its primary-
key values, encoded into a sortable string so that draining the dirty set
always produces the same processing order across repeated runs — this
determinism is what makes two different processes independently syncing
the same dirty set produce an identical, and therefore safely repeatable,
sequence of destination writes. `DirtySet.accumulate(_:)` extracts the
primary-key columns from an observed `TableChange`'s values dictionary,
logging and skipping (rather than crashing) if a conforming-but-buggy
backend ever emits a change missing one of those columns. `BlobDirtySet`
tracks the same idea for blobs, but because a blob change notification
already carries its own payload bytes, `BlobDirtySet` has no need to
re-read anything from the source at sync time — a `put` supersedes an
earlier `put` for the same key, and a `delete` supersedes a `put`,
last-write-wins.

`sync(from:to:fromCursor:)` is the operation a caller actually invokes.
It repeats the same schema gate `StorageReplicator` uses, drains both
dirty sets, re-scans each dirty row from the source (turning an
now-missing row into a destination delete rather than an upsert, since
the row was deleted at the source between the original change
notification and this re-scan), fetches only the audit events newer than
the previous cursor's watermark, and commits everything inside one
serializable destination transaction — the same fail-loud, all-or-nothing
shape `StorageReplicator` uses for a full snapshot. The retry-preservation
contract is the piece unique to the incremental path: if anything fails
after the dirty sets have already been drained, the drained keys and blob
operations are restored into the sets — awaited synchronously in the
`catch` block, never handed off to a detached background task, because a
fire-and-forget restore could race an immediate caller retry and silently
lose the very keys it was trying to preserve — so a subsequent retry
re-attempts exactly the rows and blobs the failed run did not finish.

## Rust Port and Conformance

The `rust/` directory contains a second implementation of PersistenceKit's
design for use outside the Swift/Apple ecosystem: the same closed
`StoragePredicate` algebra, the same `TypedValue` case set, the same
`Storage`/`RowStore`/`BlobStore`/`AuditLog`/`StorageObserver` trait
surface, and all three backends (`InMemoryStorage`, `SqliteStorage`,
`PostgresStorage`), plus Rust equivalents of the caching decorator, the
hashing decorator, the encryption seam, and both replication modes. The
Rust traits are synchronous (`Result<T, StorageError>`) rather than
`async`, because the Rust backends do no real asynchronous I/O of their
own; the port's `README.md` notes that Swift's `async` requirement comes
from Swift actors specifically, and a future backend that does need
asynchronous I/O could wrap its own runtime without changing the trait
shape.

Unlike LatticeLib's two legs, the Swift and Rust sides of PersistenceKit
are not gated by a shared, byte-identical conformance-fixture corpus.
PersistenceKit's cross-platform contract is a shared protocol shape and a
shared wire format — the same `TypedValue` cases, the same predicate
tree, the same identifier-validation rule — rather than a promise that
identical input always produces bit-identical output on both legs.
Each side carries its own test suite: the Rust SQLite and PostgreSQL
backends each run a ten-test conformance battery covering schema, rows,
predicates, blobs, audit, generated columns, transactions, append-only
enforcement, and introspection, exercised directly against the Rust
trait implementations rather than cross-checked against the Swift
package's own test output.
