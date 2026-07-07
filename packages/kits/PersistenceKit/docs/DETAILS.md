---
doc: DETAILS
package: PersistenceKit
repo: moot-system
authored_commit: 3c3ce06528a1d1b3b6e9aa8a6008cba20a243c23
authored_date: 2026-07-07
sources:
  - path: Sources/PersistenceKit/AuditLog.swift
    blob: ca4c0c9623056a49ad888a7a77e720a7519556bb
  - path: Sources/PersistenceKit/BlobStore.swift
    blob: f052c3ecc693d5414d57ad2c6da5fb4c5fe28f79
  - path: Sources/PersistenceKit/CacheInvalidator.swift
    blob: 0a844b8037404dd72d08df6887ceee1e8c6014f2
  - path: Sources/PersistenceKit/CachingRowStore.swift
    blob: 08c54bce31a93ae4768a7bb5ec054a39cb1d0a6f
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
    blob: 66d1e2b5629defaaaf66f9f6ae5802d28a7c8e2f
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
    blob: 45b360ff423e8c9bc294caca406771331e45e2fb
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
    blob: 98dc0350228421361e43d1a363cb81989f790c91
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLPredicateCompiler.swift
    blob: 6f0791e0372b09cf2605bae4cf149e48bbba2834
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLSchema.swift
    blob: f165f0877b96c87e2f7de46012f8f8acb3c03cc8
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLStorage.swift
    blob: 305d1983d13862e2f10176999aff2477eab0f28e
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLStores.swift
    blob: f24f390efe6066f2730f3a5b9117ec8038fd0b0d
  - path: Sources/PersistenceKitReplication/IncrementalReplicationSession.swift
    blob: 2f90378146e043d75c739d400d98c7770e07af78
  - path: Sources/PersistenceKitReplication/ReplicationTypes.swift
    blob: bb13c63d1febd50ad46c1814610d7f6c31a33112
  - path: Sources/PersistenceKitReplication/StorageReplicator.swift
    blob: f5acc5993c8647e53c127c6871a1282c24b1c427
  - path: Sources/PersistenceKitSQLite/KeychainKeyStore.swift
    blob: 0071732291a7cb6ce0777bd230a6188276fb4f32
  - path: Sources/PersistenceKitSQLite/SQLiteConnection.swift
    blob: 15b8d0631847abf343f5af6e90cff148597cfaf1
  - path: Sources/PersistenceKitSQLite/SQLiteIdentifierValidator.swift
    blob: 713339c137d6af1cfbba5a3584e05bdda70b42c5
  - path: Sources/PersistenceKitSQLite/SQLiteObserver.swift
    blob: 2cb61ed75dae5ab81a3cdfb5c9581731d25fd960
  - path: Sources/PersistenceKitSQLite/SQLitePredicateCompiler.swift
    blob: 5770adb2024c54ea6921651632ca65ee84d416af
  - path: Sources/PersistenceKitSQLite/SQLiteSchema.swift
    blob: 4ba6fc175fe9d486b17af1088a23da64041b12ae
  - path: Sources/PersistenceKitSQLite/SQLiteStorage.swift
    blob: d4b4e9c7c3229dbfd29df0453456f7b9bd2cb9c6
  - path: Sources/PersistenceKitSQLite/SQLiteStores.swift
    blob: 76499ffb70d979f0d13e8cf9e32bc38ff28ffdb5
---

# `PersistenceKit` Details

## Current Release Details

Residency is now a first-class storage choice.
`.diskBacked` is the default.
It pairs with SQLite `mmap_size` for large vector and BM25 reads.
That avoids copying whole indexes into the heap.
`.ramResident` remains available when a host wants the old low-latency cache.

Transactions now invalidate present cache entries after commit.
RowCrypto permits an empty plaintext `content` value for expunge writes.
SQLite applies 0600 permissions to the main, WAL, and SHM files.
PostgreSQL now adds the missing audit `reason` column during open.

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear grouped by
target, in the order a reader should learn them. The core protocols
and value types come first. The decorators and cross-cutting toolkits
built on top of them come next. The three backends that implement the
core protocols come after that. The replication module, which runs
across two backends, comes last.

## Target: `PersistenceKit` (core)

### Storage.swift

This file provides the `Storage` protocol, the single entry point
every backend implements. A term of art first. A protocol in Swift is
a contract that a type promises to fulfill. Any type that conforms to
`Storage` can be used anywhere `Storage` is expected. This holds
regardless of what physical engine backs it.

`Storage` bundles four sub-stores: `rowStore`, `blobStore`, `auditLog`,
and `observer`. It also bundles five life-cycle functions.
`open(schema:)` creates files or connections. It also brings the
backend up to its declared schema version. `close()` shuts the backend
down cleanly. It must be safe to call more than once.
`transaction(isolation:_:)` runs a block of work as one step. It rolls
back every change if the block throws. The two `currentSchemaVersion`
overloads report how far a backend's schema has migrated. One reports
globally; the other reports for one named kit. `migrate(to:)` advances
the schema forward. A protocol extension supplies a default isolation
level of read-committed. Most callers can therefore write
`storage.transaction { ... }` without naming a level at all.

### RowStore.swift

This file provides the typed row input and output protocol. It also
provides three small supporting types. `RowKey` is a type alias for
`UUID`. `StorageRow` is a fixed, subscriptable wrapper around a
`[String: TypedValue]` row. `RowHandle` is a `(table, key)` pair that
identifies exactly one row.

`RowStore` declares the operations every backend must support:
`insert`, `upsert`, `update`, `delete`, `query`, and `count`. Two
further query variants matter for performance and safety.
`query(...columns:)` is the column-projecting form. A caller passes a
specific list of column names. A column such as a large text blob is
then never transferred out of storage at all. This matters when a
caller only needs a handful of small columns from a wide table.
`querySkipCorrupt(...)` is the resilience form. Rather than aborting an
entire corpus scan the moment one row's stored value fails to parse,
it skips that row. It counts the skipped row and returns everything
else. The file's own comment is explicit about this method's
purpose. It exists for best-effort scans, such as scanning every
drawer in the estate. It should never be used for a single-row point
lookup, where a corrupt value should always be a loud error.

A protocol extension supplies default implementations for all of the
above. Each one falls back to the plain, full `query`. The
extension also supplies a family of as-of temporal query overloads.
As-of querying means asking what a row looked like at a past point in
time. That point is identified by an `AsOfCoordinate`, a type defined
in the `SubstrateTypes` dependency. The default implementation answers
`.present` queries normally. It throws `StorageError.featureGated` for
any `.asOf(hlc)` request. The feature stays off on purpose until two
other in-flight pieces of work both ship: lineage-wide expunge and the
erasure overlay. Turning it on early could let an as-of read resurface
content that was supposed to have been erased.

Finally, this file declares the write-transaction boundary as protocol
requirements rather than protocol-extension defaults. These
requirements are `beginTransaction`, `commitTransaction`, and
`rollbackTransaction`. This distinction matters for a subtle Swift
reason. A protocol requirement dispatches to the concrete type even
when called through an `any RowStore` existential. A protocol-extension
default does not always do so reliably. `CachingRowStore` must forward
these calls to whatever it wraps. They have to be true requirements
for that forwarding to work.

### BlobStore.swift

This file provides the blob input and output protocol: `put`, `get`,
`delete`, `exists`, `size`, and `listKeys`. A blob is a chunk of raw
bytes identified by any string key. That key is often a
content-addressed hash. It can also be a row's UUID combined with a
column name. `listKeys()` exists in particular so the replication
primitive can list every blob in a backend for a full-snapshot
copy. Key order is unspecified and may differ between backends and
even between calls. Any caller that needs a stable order, and
replication does, sorts the returned keys itself.

### AuditLog.swift

This file provides the append-only history protocol. An audit log
records every change made to a row, in order. It never edits or
removes a past entry. `append` and `appendBatch` are both idempotent on
the compound key `(eventID, hlc)`. HLC stands for Hybrid Logical Clock,
a timestamp format that orders events the same way every time across multiple
devices without perfectly matched up clocks. Replaying the same
event twice, which can happen during sync, never creates a duplicate.
`iterate(after:rowID:limit:)` walks the log in HLC order and accepts a
resume cursor. `eventsForRow(_:)` returns just one row's history. A
caller uses this to build a point-in-time projection of that row.
`PersistenceKit`'s role stops at durable, ordered storage of these
events. Enforcing the conflict-resolution rules of a CRDT belongs to a
higher kit. A CRDT is a data structure designed so concurrent edits
always merge the same way.

### StorageObserver.swift

This file provides the live change-notification protocol. It also
provides four supporting event types. `StorageEvent` is the three
kinds of row change: `insert`, `update`, and `delete`. `TableChange`
bundles one such event with its table, row key, values, and HLC.
`BlobEvent` and `BlobChange` are the blob-store matches. A `put`
event carries the written bytes. A subscriber, in particular the
incremental replication session, can then avoid a second round-trip
just to re-read what was written. `DirtyChainEvent` is a third, more
special notification. When a row in a table marked hashable is
written, the write also carries the row's new content hash. It carries
the row's two nearest ancestors in a Merkle-style containment
hierarchy. A downstream consumer needs only this to recompute an
integrity tree bit by bit. It never has to rescan the tree from
scratch.

`StorageObserver` itself declares three subscription methods:
`observe(table:events:)`, `observeBlobs()`, and `observeDirtyChain()`.
Each one returns an `AsyncStream`, a Swift type for values that arrive
over time. Delivery is documented as at-least-once. Ordering is
preserved within one subscription but not promised across different
tables. A protocol extension supplies a default no-op stream for
`observeDirtyChain()`. Older observer implementations, written before
hash-on-write existed, keep compiling without a change because of
this.

### StorageIntrospection.swift

This file provides an optional, separate capability protocol for
reporting backend health. It also provides the `StorageStats` value
type that carries the numbers. `StorageIntrospection` is on purpose
not merged into `Storage` itself. It is a distinct protocol so that
adding it never breaks an existing conformer. A caller checks for the
capability with `storage as? StorageIntrospection` rather than
assuming every backend has it.

`StorageStats` holds a superset of fields that no single backend fully
fills. Page-level statistics such as `pageSize` and
`walFrameCount` are SQLite-only. Buffer cache and transaction counters
such as `cacheHitRatio` and `deadlockCount` are PostgreSQL-only.
`rowCount` and `blobCount` are InMemory-only. A backend that cannot
supply a given field sets it to `nil` rather than fabricating a zero.
A caller can then tell "not measured" apart from "measured as zero."
The file's doc comment includes a field-by-field table. That table
cross-references exactly which backend fills which field. It is the
trusted reference for anyone adding a new statistic.

### Transaction.swift

This file provides the transaction protocol and the three isolation
levels a caller may request: `readCommitted`, `repeatableRead`, and
`serializable`. `StorageTransaction` exposes the same three sub-stores
`Storage` does: `rowStore`, `blobStore`, and `auditLog`. Code written
inside a transaction block therefore looks exactly like code written
outside one. The only difference is that every operation inside the
block either all commits together or all rolls back together. The
file's own comment notes that nested transactions and savepoints are
out of scope for this version of the design.

### StorageError.swift

This file provides the single closed error type every `PersistenceKit`
operation can throw. Being a closed `enum`, as opposed to an open
protocol, means every possible failure is listed in one place. A
caller's `switch` over a `StorageError` can therefore be exhaustive.
The cases cover backend availability, schema and migration failures,
and constraint and uniqueness violations. They also cover
connection-pool exhaustion, transaction conflicts, and type mismatches.
Three cases matter most for safety. `corruptStoredValue` fires when a
stored value could not be parsed back to its declared type. It is
thrown instead of silently using a fabricated default instead. This is because
a fabricated UUID or an epoch-zero date would be a quiet
data-identity lie. `invalidConfiguration` fires when an
`EstateConfiguration` requests something impossible on the current
platform, such as an Apple-only tagger on a non-Apple host.
`invalidIdentifier` fires when a caller-supplied SQL column or table
name contains characters outside the safe set. This is the guard that keeps a query built at run time from becoming
a SQL-injection vector. It matters even though the name is already
double-quoted.

### Column.swift

This file provides `Column`, a `(table, name)` pair used throughout
the predicate and schema types to reference one column. It also
provides `ColumnType`. This is the closed set of value kinds a column
can hold. The kinds are `uuid`, `bitmap`, `text`, `timestamp`, `float`,
`int`, `bool`, `blob`, `json`, `hlc`, and `fingerprint`. `Column`
conforms to `Comparable`, ordered first by table and then by name.
This exists purely so test fixtures and other code that sorts columns
gets a stable, steady order.

### TypedValue.swift

This file provides `TypedValue`, the tagged union that carries every
value crossing the `PersistenceKit` boundary. It is the wire format of
the whole library. Every backend pattern-matches on the case and emits
its own native representation. SQLite might store a `.uuid` as a
`TEXT` column. PostgreSQL might store it as a native `UUID` column.
Callers on both sides always see the same Swift enum. The case set is
on purpose closed. The file's own comment says adding a new case is
expected to require updating every backend. Paying this cost is worth it. It means a value works the same way
on every backend. A new value kind can never partially work on only
some backends. Two of the twelve cases, `.hlc`
and `.fingerprint`, exist for a specific reason. `PersistenceKit` is
forbidden from building again substrate math. The `HLC` and
`Fingerprint256` types themselves come from the `SubstrateTypes`
dependency. `PersistenceKit` only needs a slot to carry them.

### Predicate.swift

This file provides `StoragePredicate`, the closed query-condition tree
every backend compiles to its own query language. It also provides
`OrderClause` and `OrderDirection` for sorting. The predicate cases
fall into three families. The logical family covers `and`, `or`,
`not`, `isTrue`, and `isFalse`. The comparison family covers `eq`,
`neq`, `lt`, `lte`, `gt`, `gte`, `isNull`, `isNotNull`, `in`, and
`like`. The bitmap family covers `bitmaskAll`, `bitmaskAny`,
`bitmaskNone`, and `bitwiseEq`. All four apply only to integer-family
columns. `PersistenceKit` treats a predicate as opaque data except when
compiling it. No backend ever needs to special-case another backend's
query shape. The tree is the same tree everywhere, so there is nothing
to special-case.

The two static helpers `all(_:)` and `any(_:)` build an `and`/`or`
tree from a list of predicates. Both include useful short-circuiting.
An empty list of conditions to AND together collapses to `isTrue`, an
always-true filter, because "no restrictions" should match everything.
An empty list to OR together collapses to `isFalse`. A list that
already contains a conflicting trivial case collapses the whole
expression right away. This happens when `isFalse` sits inside an
`all`, or when `isTrue` sits inside an `any`, rather than building a
needlessly large tree.

### GeneratedColumn.swift

This file provides first-class computed columns. A computed column
holds a value derived from other columns in the same row. A small,
structured integer expression performs that derivation, rather than a
caller storing the value directly. `GeneratedColumn` names the column,
its result type, and its `GeneratedExpression`. `GeneratedExpression`
is a closed, recursive enum. It covers exactly the bit-field algebra a
bitmap-heavy schema needs: reading another column (`.column`), a
constant (`.literal`). Three cases cover bitwise AND, OR, and XOR.
Two more cases cover a left shift and a right shift by a fixed amount.
A final pair of cases test equality and inequality, each one giving
back one or zero. The file's own comment explains why this is a structured
expression tree rather than a raw SQL string. A SQL string generated
column would push backend-specific syntax into a schema declaration.
The in-memory backend also has no SQL engine to evaluate it against. A
structured expression has exactly one meaning. SQLite and PostgreSQL
render it to native `GENERATED ALWAYS AS (...) STORED` DDL. The
in-memory backend works out it directly. These are three faithful
versions of one description. No backend gets an escape hatch to
interpret it differently from another.

`renderSQL()` turns an expression into a SQL fragment shared by SQLite
and PostgreSQL. Both use the same bitwise-operator syntax and
double-quoted identifiers. The one exception is bitwise XOR, which
SQLite lacks as an operator. Both backends instead render it as `(a | b) - (a & b)`, which gives
the same value using only AND and OR. `evaluate(_:)` computes
the same result directly against an in-memory row dictionary, for the
in-memory backend. `integerValue(_:)` extracts an `Int64` from any
integer-family `TypedValue`: an `.int`, `.bitmap`, `.bool`, or `.hlc`.
It returns zero for anything else or for an absent column. That zero
is the in-memory evaluator's sentinel for "no meaningful value here."

### EstateConfiguration.swift

This file provides `EstateConfiguration`, the one value that fully
describes how to open one estate's storage. It also provides
`BackendConfiguration`. This is the closed choice of physical engine:
`sqlite`, `postgresql`, or `inMemory`. It also carries that engine's
connection settings. `EstateConfiguration` itself carries the estate's
encryption mode, its cache setup, and its novel-token tagger choice.
Each of these has a default. Existing code that never mentions these
newer fields keeps compiling and keeps behaving exactly as it did
before they existed. A caller who asks for nothing else gets a
plaintext, uncached, HMM-tagged estate.

`queueSibling(filename:)` is a more special function. It derives a
second `EstateConfiguration` that points at a companion database file
sitting beside the estate's own file. A work-queue kit, for example,
gets its own small database this way. It needs no separate key
distribution and no separate configuration path. Take a SQLite estate
at `<dir>/<uuid>.sqlite`. Asking for the sibling `"queue.sqlite"`
produces `<dir>/<uuid>.queue.sqlite`. The estate's own file stem is
folded into the sibling name. Two different estates in the same
directory can therefore never collide. For an in-memory estate, the
sibling is also in-memory. Both are ephemeral, which is the correct
pairing for tests. For a PostgreSQL estate, the function throws
`StorageError.featureGated`. The design puts off a PostgreSQL queue
sibling on purpose until later. It fails loudly rather than quietly
returning a half-working configuration.

The private helper `deriveQueueSiblingID(parentID:filename:)` computes
the sibling's estate ID in a fixed way. It XOR-folds the filename's
UTF-8 bytes into a sixteen-byte tag. It then XORs that tag with the
parent UUID's raw bytes. The same parent estate and the same filename
therefore always produce the same sibling ID. No call to `UUID()`
happens anywhere on this path. This fixed behavior matters. Every
process that opens the same estate must agree on the same sibling ID
without talking to any other process.

### EstateCacheConfig.swift

This file provides `EstateCacheConfig`, the small value type that
turns row caching on or off for one estate. It also bounds how much a
cache is allowed to hold. It bounds how sensitive the content it holds
may be. `ceilingBytes` is clamped to be non-negative at construction.
`sensitivityThreshold` is clamped to at most two. Sensitivity level
three is called "Secret" in the ARIA adjective scale this kit shares
with the rest of the system. Secret content must never be cached, no
matter how the estate is set up. Clamping at construction enforces
that rule. No caller needs to remember the numeric boundary on its
own. `EstateCacheConfig.disabled` is the zero-change default. An estate
that never mentions caching gets exactly the pre-caching behavior.

### EncryptionMode.swift

This file provides the three at-rest encryption modes. It also
provides the configuration value that carries one of them per estate.
`EncryptionMode.plaintext` stores content as-is. Encryption, if any,
happens only at a sharing boundary elsewhere in the system.
`.rowEncryption`, Mode 2, encrypts one column's content per row under a
key named by the row's own `keyID` column. `.fullDatabase` is Mode 3.
It encrypts the entire SQLite file, including its schema, through
SQLCipher's `PRAGMA key` at the connection layer. This makes the per-row seam a
no-op for that mode. The whole file is already ciphertext on disk, so
there is nothing left for the per-row seam to do. A fourth mode is on
purpose left out of this enum: database-plus-threshold encryption for
a FedRAMP-tier deployment. The file's comment stresses that this build
does not have that capability at all. Adding it later is a conscious,
reviewed act, not an accident.

`EstateEncryptionConfig` bundles the mode with a key identifier and the
actual `SymmetricKey`. Its stored `key` property is `package`-scoped
rather than `public`. The SQLite backend, in a sibling module of the
same Swift package, needs it. Nothing outside the package should ever
see it. The convenience initializer `init(_ mode:)` mints a fresh,
full-entropy 256-bit key and a UUID key identifier for either
encrypting mode. It mints neither for plaintext. `usesRowCrypto` is
`true` only for Mode 2. Mode 3 protects everything at the file level,
so the per-row seam has nothing to do there. `fullDatabaseKeyHex`
renders the whole-file key as lowercase hex for the SQLCipher `PRAGMA
key` statement. It is documented as something that must never be
logged.

### RowCrypto.swift

This file provides the per-row AES-GCM-256 encryption used by
`EncryptionMode.rowEncryption`. It also provides the three write and
read seam functions every backend calls at the same two moments: just
before a write, and just after a read. Living in `PersistenceKit` core
rather than in one backend module is the point. Both
`PersistenceKitSQLite` and `PersistenceKitPostgreSQL` call the exact
same code. The two backends therefore produce byte-compatible
ciphertext envelopes without either one knowing about the other.

`AeadProvider` is a small protocol seam. AEAD stands for Authenticated
Encryption with Associated Data. This is a class of algorithm that
both encrypts data and proves no one changed it. Any type that
conforms to `AeadProvider` can swap in for the default algorithm
without touching any call site. The file names this as the point
where a future FIPS-checked provider would plug in.
`CryptoKitAeadProvider` is the default version, backed by Apple's
CryptoKit. It makes a fresh random ninety-six-bit nonce on every
single encrypt call. Reusing a nonce under the same key is the one
mistake that breaks AES-GCM's whole guarantee. It returns the three
parts joined as `[12-byte nonce][16-byte tag][ciphertext]`. A later
decrypt is self-contained from the stored bytes alone. It has nothing
else to look up.

`encryptedForWrite(_:config:provider:)` is the write-side seam. For an
estate using row encryption, it encrypts the row's `content` column. It
stamps a `keyID` column recording which key sealed it. For any other
mode, or for a row with no `content` column, it returns the values
unchanged. `decryptedForRead(_:config:provider:)` reverses this on
read, but only when the row's stored `keyID` matches the estate's own
key identifier. A mismatched `keyID` means the row was sealed under a
key this estate does not hold. The function passes the row through as
still-ciphertext rather than trying a decrypt that would only fail as
an authentication error. `assertContentKeyIDInvariant(_:table:config:)`
is a final structural guard. On an encrypting estate, any
content-bearing row that reaches a backend write path with `.text`
content and no `keyID` means the encryption seam did not run somewhere
upstream. The function throws rather than let that plaintext be stored
where a later read could never recover it.

### CachingRowStore.swift

This file provides `CachingRowStore`, a decorator that wraps any
`RowStore` and serves an in-memory hot tier of recently-read rows. The
caller never has to know this layer is there. The file's own comment
states the guarantee plainly. Every operation returns results the same
as what the unwrapped backing store would have returned. The cache
changes only latency, never correctness.

The cache key is not just `(table, row key)`. It is that pair combined
with an `AsOfCoordinate`. A `.present` (current-state) read and an
`.asOf(hlc)` (past-state) read of the same row are cached as two
entirely separate entries. A snapshot read against a pinned, fixed
past state can be cached forever. The GC pin mechanism guarantees that
data will not be swept away out from under it. A present-state read,
by contrast, must be dropped the instant the row changes. Four write
methods drop the affected present-state entry after the underlying
write succeeds: `insert`, `upsert`, `update`, and `delete`. For a
batch predicate that does not name one specific row, these same four
drop every present-state entry for that table instead. An optional `parentChainProvider` closure,
supplied by the calling kit, lets a write also drop cached Merkle-
aggregate entries for that row's ancestors. A cached rollup value never
goes stale just because one leaf changed underneath it.

Admission to the cache is gated by sensitivity. `isAdmissible(_:)` reads
a row's `provenance` bitmap column, when present, and decodes a
six-bit sensitivity field at bits thirty through thirty-five. It maps the scale-gapped raw values zero, sixteen, thirty-two, and
forty-eight to four ordinals. The ordinals are Normal, Elevated,
Restricted, and Secret, in that order. Secret content is turned away
without exception. Anything above the
configured `sensitivityThreshold` is turned away too. An unrecognized
bit pattern, or a `provenance` value of the wrong `TypedValue` case, is
also turned away. The comments call this failing closed. Any doubt
about a row's sensitivity keeps it out of the cache, rather than
risking that sensitive content sits unencrypted in process memory
longer than it should. Eviction removes the least-recently-used entry first, whenever the
estimated byte total goes over the ceiling the estate sets. A counter
that only ever rises tracks recent use. The cache does not track
wall-clock time at all.

All of this changeable state lives inside a private `actor`,
`CacheActor`. The state is the entry dictionary, the access counter,
and the running byte total. Every stored property of the actor is
itself `Sendable`. Wrapping the state in an actor is what lets the
outer `CachingRowStore` class safely conform to `Sendable` under Swift
6's strict concurrency checking. `CachingRowStore` itself is a `final
class` with no locks of its own.

### CacheInvalidator.swift

This file provides `CacheInvalidator`, the bridge between a
`StorageObserver`'s live change stream and a `CachingRowStore`'s
invalidation method. This bridge is needed for the case where some
other writer bypasses the specific `CachingRowStore` instance
entirely. That writer might be a second process, or a raw connection
opened for a migration. Without this bridge, the cache would serve
stale data forever after such a write, because it would never learn
the write happened.

One `CacheInvalidator` watches every table it is told to watch through
a single detached background task. That task fans out one child task
per table, using Swift's structured-concurrency `withTaskGroup`.
`cancel()` cancels the whole tree at once. `deinit` calls `cancel()` on
its own. A caller who simply lets the invalidator go out of scope does
not leak a background task. The comment on `init(cache:observer:tables:)`
flags one narrow race. Subscriptions start inside the background task.
A write issued in the same instant as `init()` could in theory race
ahead of that subscription. A caller with strict-ordering needs should
yield briefly, for example with `Task.sleep`, before trusting that the
very first write was seen.

### HashingRowStore.swift

This file provides `HashingRowStore`, a decorator that intercepts
writes to any table marked hashable in the schema. It computes a
content hash for the row. It emits a `DirtyChainEvent`, so an
integrity tree elsewhere in the system can stay current bit by bit,
rather than being rebuilt from scratch on every write.
`PersistenceKit` on purpose does not import a hashing library itself.
The actual hash function arrives as an injected `ContentHashProvider`
closure supplied by the calling kit, which does import the hashing
library. This package stays free of that dependency while still owning
the mechanism that wires hashing into every write path.

The trickiest part of this file is keeping the hash correct on partial
writes. An `insert` always carries the row's full column set, so
hashing the incoming `values` directly is correct. An `update`,
however, often carries only the columns being changed. Hashing just
that partial dictionary would produce a hash for a row that never
actually existed in storage.
`augmentWithHashForKnownKey(table:rowKey:mergedValues:)` solves this by
pre-reading the row's current, full state. It merges the incoming
changes on top of it. It then hashes that merged result. `upsert` uses
this same approach when it resolves to an update against an existing
row rather than a fresh insert. Before hashing, the function strips
any existing `content_hash` column from the input. The insert path and
the update-via-merge path therefore always hash the same set of data
columns. Neither one ever drifts apart just because it happened to
already carry a stale hash value.

`emitDirtyChain(table:rowKey:hashResult:)` delivers the resulting
`DirtyChainEvent` to an injected `ObserverRegistryRef` closure, which
is `nil` for backends without observer support. The hash is still
computed and stored either way. The notification step is simply
skipped when there is no closure to call.

### ErasureLedger.swift

This file provides the append-only record of what has been erased,
without ever storing the erased content itself. A "drawer," in this
context, is one unit of stored content. The term comes from the
estate's own filing metaphor, not from this package.
`ErasureLedgerEntry` pairs a `drawerId` with the `HLC` at which it was
erased. The `erasure_ledger` table this module declares is marked
append-only in its `TableDeclaration`. Every backend enforces this at
the storage layer. Even a caller that somehow bypasses the ledger's
own API cannot change or remove an entry once it is written. This is
exactly the tamper-evidence property an erasure record needs.

`recordErasure(rowStore:drawerId:erasedHlc:)` inserts one entry. It
throws `StorageError.duplicateKey` if that drawer was already erased,
because each drawer is erased exactly once.
`isErased(rowStore:drawerId:)` is the fast point-lookup used on every
read that might need to hide erased content.
`lookupErasure(rowStore:drawerId:)` returns the full entry when a
caller needs the erasure time itself, not just a yes-or-no answer.

### ErasureOverlay.swift

This file provides the two-phase, fail-closed filter that actually
hides erased content from a query result. It builds on top of the
ledger, though this module does not itself define that ledger.
`ErasureOverlayConfig` is supplied by a higher kit. It carries two
pieces of entity-specific knowledge `PersistenceKit` does not have on
its own. `extractErasureId` is a closure that pulls the erasure-ledger
key out of a result row, or returns `nil` for a row type not subject
to erasure at all. `contentColumns` is the list of column names to
null out when a row turns out to be erased.

`ErasureOverlay.apply(rows:config:rowStore:)` runs the two phases the
file describes at the top. Phase one is a plain query that
already ran before this function was called. Phase two walks the
returned rows one at a time. It checks each row's erasure ID against
the ledger. It nulls that row's content columns if the row was
erased. The important design choice is what happens when the ledger
check itself throws an error, instead of giving a clean yes-or-no
answer. The row is dropped from the result. This is the
fail-closed half of "two-phase fail-closed." An uncertain answer about
whether a row was erased is treated the same as "it was." Showing
content that should have been erased is a worse failure than
temporarily hiding content that was not.

### GCPin.swift

This file provides the query that tells a garbage-collection pass
which rows it must not touch yet. GC, short for garbage collection, is
any maintenance pass that reclaims space by deleting rows no longer
needed. An old version of a row that a newer version has replaced is
one example. The problem this file solves is simple to state. A
snapshot taken earlier, through `SnapshotRegistry`, might still need to
read an "old" row that a GC pass would otherwise think it safe to
delete.

`GCPin.minimumRetainableHlc(rowStore:)` answers this by finding the
smallest HLC across every currently-registered snapshot. The oldest
live snapshot's time stamp is the pin. Any row whose HLC sits at or
after that pin must survive a GC pass. Anything strictly older is safe
to reclaim. When no snapshots exist at all, the function returns
`nil`. Nothing is pinned in that case, and every row is fair game for
GC. `isPinned(rowStore:rowHlc:)` is the convenience per-row check built
on top of the same query.

### SnapshotRegistry.swift

This file provides the durable record of named point-in-time
snapshots. It also provides the signed proofs that go with them. A
snapshot, in this design, is not a copy of any data. It is a registry
row recording one HLC, the moment the snapshot was taken. It also adds
one or more proof rows. Each proof row records what a Merkle root
looked like at that moment for a given subject. `PersistenceKit` itself knows nothing about what a "subject"
is. The `subjectKind` and `subjectId` strings are supplied by whichever
higher-level kit is taking the snapshot.

`SnapshotId` is a UUID-backed opaque ID, minted fresh by
`SnapshotId.mint()`.
`SnapshotRegistryOps.createSnapshot(rowStore:hlc:label:createdAt:attestations:)`
mints an ID, inserts one registry row, and inserts one proof row per
subject passed in. It returns the finished `SnapshotRecord` to the
caller. `listSnapshots(rowStore:)` walks every registered snapshot in
HLC order. `deleteSnapshot(rowStore:snapshotId:)` removes a snapshot's
proof rows before removing its registry row, child rows first, so no
orphaned proof can ever point at a deleted registry entry. It reports
whether anything existed to delete. `attestations(rowStore:snapshotId:)`
reads back every proof for one snapshot, ordered by subject for a
steady result. The remaining private helpers handle the mechanical
work. They turn a `StorageRow` into a typed `SnapshotRecord` or
`SnapshotAttestation`, and back again.

### NoOpObserver.swift

This file provides `NoOpObserver`, a plain `StorageObserver` that
satisfies the protocol without doing any real work. Its three
subscription methods each return a stream that finishes right away,
delivering nothing. It exists as a stand-in for any backend or test
path that has no real change-notification mechanism yet. That backend
still needs to satisfy the `Storage` protocol's requirement for an
`observer` property. The PostgreSQL backend, for instance, uses this
type directly. It does not implement a live PostgreSQL notification
channel.

### NovelTokenTaggerChoice.swift

This file provides the estate-creation-time choice of which tagger
classifies a "novel token." A novel token is a word not found in a
language model's own word list. An estate needs to sort words like
this somewhere in its processing pipeline. The two
cases are `.hmm` and `.nlTagger`. `.hmm` is a fixed Hidden Markov Model
tagger. It produces the exact same output on every platform, so it is
safe to use across a group of devices sharing memory. `.nlTagger` is
Apple's `NaturalLanguage` framework tagger. It can be more accurate on
Apple hardware. It does not work outside Apple platforms. It also does not always produce the same output across different
OS versions.

This choice is fixed forever at estate creation in this version of the
design. Changing it later, with re-tagging of existing content, is on
purpose put off to a future version. The file's long comments spell
out the practical result. An estate created with `.nlTagger` cannot
safely federate with an `.hmm`-tagged estate without re-tagging all of
its content first. Federating means sharing and comparing memories
across estates. The two taggers can disagree on the same word. No
code enforces that rule on its own yet. A caller who picks
`.nlTagger` must keep that estate out of any federation with a
mismatched estate. `NovelTokenTaggerChoice.default` is `.hmm`. This is
the safe, federation-friendly baseline every estate gets, unless a
caller on purpose picks the Apple-only choice instead. An enum with
the same name exists in `LatticeLib`, a different package, defined on
its own. The two packages on purpose do not share this one type. Not
one imports it from the other, so neither package depends on the
other.

### PersistenceKitTelemetry.swift

This file provides `reportStorageStats(_:estateID:now:)`. This function
turns a `StorageIntrospection.stats(now:)` snapshot into a stream of
named metrics through `IntellectusLib`, a light, zero-dependency
telemetry library. The function's whole behavior is gated on
`Intellectus.isEnabled`, which defaults to `false`. Telemetry is off
for nearly every deployment. When it is off, the function returns
after a single atomic boolean check. It never calls `stats(now:)`. It
never builds a single metric object. The file's own comment puts this
cost at roughly one nanosecond. The disabled path is close to free.

When telemetry is on, the function emits one metric per non-`nil`
field of the captured `StorageStats`. Each metric sits under the
`persistence.db.*` namespace. Each one is tagged with the emitting
kit's name and the estate's ID, so a monitoring backend can filter and
group by estate. `StorageStats` fields are `nil` exactly where a
backend cannot supply them. The set of emitted metrics differs from
backend to backend. This function needs to know nothing about which
backend produced the snapshot. For example, an in-memory estate never
emits a `wal_frames` metric. That field was simply never filled in.
Every time stamp passed to `Intellectus.report(_:)`
comes from the caller-supplied `now` argument. It never comes from a
fresh `Date()` call inside this function. This is a rule followed
through the whole package. Any code that might run the same way twice
must never read the wall clock itself.

## Target: PersistenceKitInMemory

### InMemoryStorage.swift

This file provides `InMemoryStorage`, the backend used for tests and
for any storage that should live only as long as the current process.
It also provides the `InMemoryStateActor` that owns every piece of
changeable state behind Swift's actor isolation. There is no file on
disk. There is no network connection. Every table is a plain Swift
dictionary held in memory.

The trickiest part of this file is how
`transaction(isolation:_:)` implements rollback. It does not run the
caller's block against a private, separate copy of the state and then
replace the live state only on success. Instead, it runs the block
directly against the live actor state. It takes a snapshot copy only
for the error path. The file's own comment explains why the simpler
"copy, change, replace on success" approach is wrong. Say
some other write reaches the live actor state between the moment the
snapshot was taken and the moment a successful transaction would
replace the whole state with its snapshot-derived copy. That
concurrent write would be silently wiped out by that swap. The
comment traces this exact bug to a real incident. A burst of
concurrent inserts racing a transaction lost five to ten percent of
queued work. Running against live state avoids the problem.
The cost is needing a clear rollback-to-snapshot path when the block
throws.

Reports of a change during a transaction are held back, rather than
sent right away, for a related reason. Say a subscriber were told
about a write that the transaction later rolled back. From the
estate's point of view, a rolled-back write never took place at all.
`beginNotificationBuffering()` starts collecting change reports
instead of sending them. `commitNotifications()` flushes the buffer to
observers only after the block has returned with success. The
rollback path throws the buffer away instead. Four operations change
stored rows: `insertRow`, `upsertRow`, `updateRows`, and `deleteRows`.
Each one works out any declared `GeneratedColumn`
values through the static `materializeGenerated(_:_:)` helper before
storing the row. This matches what SQLite and PostgreSQL compute on
their own, so a query against any backend returns the same generated
values. `queryRows(...)` applies the predicate and ordering against
full rows first. It only narrows to the requested column set at the
very end. A projected query can still filter or sort on a
column it does not actually return. This matches SQLite's own
`SELECT`-list-versus-`WHERE`/`ORDER BY` behavior, in full.

`InMemoryStorage` also conforms to `StorageIntrospection`. It reports
a rough byte size, a live row and blob count, and a steadily rising
rollback counter. The byte size is a flat per-row guess plus exact
blob byte counts. The file's own comment calls this a rough signal,
not an exact count of memory used.

### InMemoryRowStore.swift

This file provides the thin `RowStore` conformance that simply forwards
every call to the shared `InMemoryStateActor`. Its one piece of design
worth noting is the column-projection overload. The in-memory backend
already holds the full row in memory, so leaving out columns nobody
asked for saves no real transfer cost, the way it does for SQLite. The
comment is clear that the point of building it anyway is one of
matching shape. A `StorageRow` returned from a projected in-memory
query is shaped just like the same projected query against SQLite.
Code under test cannot depend on a column being present by mistake,
just because the in-memory backend happened to still have it in
memory.

### InMemoryBlobStore.swift

This file has the `BlobStore` conformance for the in-memory
backend. It is five one-line forwarding methods to the shared state
actor's blob dictionary. There is nothing backend-specific to explain
here beyond what `BlobStore.swift` already covers. This file exists
purely to satisfy the protocol against the actor's storage.

### InMemoryAuditLog.swift

This file has the `AuditLog` conformance for the in-memory
backend. It is again a thin forwarding layer to the shared state
actor. That actor removes duplicates on `(eventID, hlc)`, just as the
protocol's idempotence contract requires.

### InMemoryObserver.swift

This file provides `ObserverRegistry`. This is the subscription
bookkeeping shared by every in-memory row store, blob store, and
audit log instance. All three sit behind the same state actor. It also provides
`InMemoryObserver`, the thin `StorageObserver` conformance built on
top of it. The registry on purpose does not use actor isolation for
its subscriber lists. Instead, it uses a plain `NSLock`. The file's
comment lays out the trade-off in plain terms. Registering a
subscription must happen right away, with the stream already recording
it before `observe()` returns to the caller. A change reported the
very next line of code can then never race ahead of the subscription
being recorded. An `actor`-based registry would force `register` to be
an `async` function. That would reopen that same race all over again. This
mirrors the matching Rust observer hub, which registers right away for
the same reason.

`notify(_:)` takes a snapshot of the list of matching subscribers
under the lock. It then sends to each of them outside the lock. A
subscription's own end-of-life handler, which also needs the lock to
remove itself from the list, can never deadlock against a report
already in flight. Row, blob, and dirty-chain
subscriptions are tracked in three separate dictionaries. All three
follow the same pattern: register right away, snapshot inside the
lock, and send outside the lock.

### PredicateEvaluator.swift

This file has the in-memory reader for `StoragePredicate`. It is
the only backend that works out the predicate tree directly against
Swift values in memory, rather than compiling it to a query string
first. `PredicateEvaluator.evaluate(_:against:)` walks the tree from
the top down. It handles every case from `Predicate.swift` by reading
the named column out of a `[String: TypedValue]` row, treating a
missing column the same as an explicit `.null`. It then applies the
matching comparison, bitmask test, or `LIKE`-style text match.
`likeMatch(_:pattern:)` turns a SQL `LIKE` pattern into an
`NSRegularExpression`. In that pattern, `%` stands for any run of
characters. `_` stands for just one character. The in-memory backend
matches the same patterns SQL's `LIKE` operator would.

`TypedValueComparator.compare(_:_:)`, in the same file, is the shared
ordering function used both by predicate comparisons and by `ORDER
BY` sorting. `nil`, that is `.null`, sorts before every other value.
Two values of the same case compare by their underlying Swift value.
`.hlc` is compared by its packed integer form instead. That form is
`HLC.packed`, defined at the bottom of `InMemoryStorage.swift`. Packing
gives a single total order over an HLC's three parts.

## Target: PersistenceKitSQLite

### SQLiteStorage.swift

This file has `SQLiteStorage`, the `Storage` conformance for the
on-device backend. It also has the much larger `SQLiteBackend` actor
that does almost all of the real work. One actor per estate handles it all: schema migration, row-and-blob
work, audit logging, and health checks. The file's opening comment
matches this design: a
SQLite estate gets exactly one connection. SQLite's own WAL, or
write-ahead log, mode handles concurrent readers safely underneath.

Schema migration walks a schema's declared `Migration` list. It runs
each pending migration inside its own `BEGIN IMMEDIATE`/`COMMIT` pair.
It rolls back and throws `StorageError.migrationFailed` if any step in
that migration fails. `applyMigrations(_:)` is written to be safe to
call even on a brand-new SQLite file, one never opened through
`openSchema(_:)` first. This is a real case in practice, since at
least one caller calls `migrate(to:)` directly. It handles this by
re-running the safe-to-repeat table- and migrations-table-creation
steps before it checks what actually needs to migrate.

`runTransaction(isolation:_:)` waits for an already-open transaction
on the same actor rather than failing right away. The block it runs
is async, so the actor can suspend inside it. A second caller can then
reach this method while the first transaction is still open. That is
ordinary contention between two background workers. A Merkle rollup
and a live capture racing each other is one example. It is not a
programming error, so `runTransaction` no longer throws on sight. It
polls every twenty-five milliseconds instead, for up to sixty seconds
total. Only a wait that runs out the whole span throws
`StorageError.transactionConflict`. A true nested call, one that
reenters `runTransaction` from inside its own block, still fails this
way rather than hanging forever. The older, explicit
`beginTransactionDirect()` path keeps its immediate throw. It is a
synchronous method and cannot wait on anything.

Five row-changing methods start by calling `validateSQLIdentifier`
on every table and column name they are about to place into a SQL
string: `insertRow`, `upsertRow`, `updateRows`, `deleteRows`, and
`queryRows`. The file's comments tie this directly to a
named security fix, SECFIX-WS2-PK F9. Double-quoting a SQL name is not enough protection on its own. A name
might hold a double-quote character. That one character can escape
the mark and change the query. Every name is checked against a
strict allow-list before it is ever placed into a SQL string.
`insertRow` and `queryRows` are also where the at-rest row-encryption
seam actually runs. `insertRow` calls `encryptedForWrite` before
binding values. `queryRows` calls `decryptedForRead` after reading
them back. Both are no-ops on a plaintext or whole-database-encrypted
estate.

Four more methods reuse this same cache: `queryRowsSkipCorrupt`,
`fetchMatchingRowKeys`, `countRows`, and `iterateAudit`. All nine call
`connection.prepareCached(_:)` in place of `connection.prepare(_:)`.
`SQLiteConnection.swift`, covered next, describes the statement cache
this feeds. The same SQL text repeats call after call in a bulk loop,
such as a large import. Parsing it fresh every time was a measured
hot spot. Reusing a parsed statement removes that cost.

`readColumn(stmt:index:schema:columnName:table:)` is the file's most
carefully reasoned function. It decides, column by column, whether a
raw SQLite value should be trusted as-is or treated as corrupt. The
comment draws a line between two cases. One is a type-tolerant decode,
a valid value stored under SQLite's flexible column affinity, which is
passed through as-is. The other is a genuine parse failure on a
column whose declared type says it should parse, such as an
unparseable UUID or time-stamp string. That second case throws
`StorageError.corruptStoredValue` rather than ever making up a random
UUID or an epoch-zero date in its place.
`queryRowsSkipCorrupt(...)` reuses the same column-reading logic but
catches exactly that error per row. It logs the error, counts the row,
and moves on to the next one. This cursor-level version can do
something `RowStore`'s protocol default can only approximate, which is
to discard an entire query's results.

`storageStats(now:)` gives `StorageIntrospection` for SQLite by
reading a handful of read-only `PRAGMA` statements: `page_size`,
`page_count`, and `freelist_count`. It works out the WAL frame count
straight from the `-wal` side file's size on disk. It does not call
`PRAGMA wal_checkpoint` for this. That pragma can fail with a locked
error if some other job is in flight. This can happen even from
inside the same serializing actor.

### SQLiteConnection.swift

This file has `SQLiteConnection`, a thin Swift wrapper around the C
`sqlite3` API. It also has `SQLiteStatement`. This is the
prepared-statement wrapper used for every parameterized query. A
third piece, `ISO8601`, is the shared time-stamp formatting and
parsing tool. It runs everywhere a `.timestamp` `TypedValue` crosses
the SQLite boundary.

Opening a connection does several things, in a specific order that
matters for safety. First, it checks whether the target path is a
symbolic link. It refuses to open the file if so. The comment names
this CAND-052, a defense against a pre-planted symlink that could
otherwise send SQLite's writes to some other file on disk. The check
uses `lstat` behavior, through `resourceValues(forKeys:)`, so it spots
the symlink itself rather than following it to whatever it points at.
Second, if the estate uses whole-database encryption, it issues
`PRAGMA key` with the estate's hex-coded key before any other
statement runs. This must be the very first statement. SQLCipher
cannot even read the schema on page one of the file without the
right key already applied. Third, it applies Apple's file-level Data
Protection, a best-effort step that layers OS-level at-rest
protection on top of SQLCipher's own encryption. It sets the durability pragmas last: WAL journal mode, `NORMAL`
synchronous durability, a WAL auto-checkpoint limit, a busy timeout,
and foreign-key rules.

`SQLiteConnection` also owns a per-connection cache of prepared
statements, capped at one hundred twenty-eight entries.
`prepareCached(_:)` looks up that cache by the raw SQL text. On a
repeat call with the same text, it returns the cached statement
instead of parsing fresh SQL. That returned statement already has its
old bindings cleared and its execution state reset. The cache
checks a statement out while a caller holds it, the way the Rust
port's `rusqlite` cache does. A cached `SQLiteStatement` returns
itself to the cache when its own `finalize()` runs, instead of being
destroyed outright. A call site's existing `defer { stmt.finalize() }`
line needs no change to gain this reuse. Going past the cache's cap
destroys and rebuilds the whole cache at once, a crude but
constant-time way to stay bounded. `close()` destroys every cached
statement first, before it calls `sqlite3_close_v2`. SQLite defers the
real close while any prepared statement on the connection still
exists, so skipping this step first would leave a zombie connection
behind.

`ISO8601` deserves its own close look. Two very different stories
about speed and correctness live inside it. `string(from:)` first pins an out-of-range `Date` into the range
`ISO8601DateFormatter` can parse back: the years 0001 through 9999.
One example of an out-of-range date is one built by mistake from
milliseconds read as seconds. Writing an unparseable string like `+59009-...` would silently break
any future read of that row. A pinned value with a logged warning is
judged better than a value nothing can ever read back right. `date(from:)` first tries a hand-written, no-allocation parser:
`fastParseCanonicalUTC(_:)`. Only then does it fall back to the much
slower, ICU-backed `ISO8601DateFormatter`. The comment explains why the fast
path exists at all. A stack-sampling profile during a large import
showed the formatter's `date(from:)` using roughly eighty percent of
total CPU time. A Merkle rollup re-decodes every row's time stamp on
every insert, which makes the parse cost grow with the square of
import size. The fast parser knows only the exact canonical shape
this kit itself writes. It returns `nil` for anything even slightly
different: a numeric time-zone offset, lowercase letters, or trailing
junk. That sends control back to the slow, fully general formatters,
so correctness never slips. The fast path is checked byte-for-byte
against the formatters in its own test suite.

### SQLiteStores.swift

This file has `SQLiteRowStore`, `SQLiteBlobStore`, `SQLiteAuditLog`,
and `SQLiteTransaction`. These are four small wrapper types that each
forward to the shared `SQLiteBackend` actor. They give the actor's own
methods their public `RowStore`, `BlobStore`, `AuditLog`, and
`StorageTransaction` faces. `SQLiteRowStore.querySkipCorrupt(...)` is
the one method here with real logic of its own. Rather than falling
back to `RowStore`'s protocol-extension default, which can only throw
away an entire failed query, it calls
`SQLiteBackend.queryRowsSkipCorrupt(...)` directly. A corrupt row found
partway through a large corpus scan is skipped and logged on its own,
rather than aborting everything already read.

### SQLiteSchema.swift

This file has `SQLiteSchema`, the enum of pure functions that turn a
`SchemaDeclaration` into SQLite's own DDL. DDL stands for data
definition language, the SQL used to build and change tables, as
opposed to the SQL used to read and write rows. `nativeType(_:)` maps
every `ColumnType` case to its SQLite storage class. Most map
directly, but `.uuid` and `.timestamp` both map to `TEXT`, because
SQLite has no native type for either. `.hlc` maps to `INTEGER`,
because it is stored as a packed sixty-four-bit value. `.fingerprint`
maps to `BLOB`, because it is stored as raw bytes. `createTable(_:)`
puts together one `CREATE TABLE IF NOT EXISTS` statement. It covers
plain columns, generated columns, a primary key, and unique rules.
Generated columns are always rendered `STORED`, to match PostgreSQL's
lack of a `VIRTUAL` form. `appendOnlyTriggers(_:)` emits a `BEFORE UPDATE`/`BEFORE
DELETE` trigger pair for any table marked append-only. Each trigger
stops the statement outright with `RAISE(ABORT, ...)`. This is the
real enforcement behind, for example, the erasure ledger's
append-only rule.

This file also owns the three bookkeeping tables every SQLite estate
carries, no matter what schema a caller declares. `_storagekit_migrations`
tracks each kit's applied schema version. `_storagekit_audit` is the
one source of truth for every audit event. It stores full-precision HLC columns alongside the packed integer
form. The packed form loses a bit of the physical-time field for
dates far enough in the future. The full-precision columns exist so that a cold rebuild
of an estate's last-known HLC never quietly disagrees with what a
snapshot first recorded. `_storagekit_blobs` is the flat key-and-bytes
table backing `SQLiteBlobStore`.

### SQLitePredicateCompiler.swift

This file has the translation from a `StoragePredicate` tree into a
parameterized SQLite `WHERE` clause, plus its ordered list of bound
values. `compile(_:)` is the public entry point. The private recursive
`render(_:bindings:)` walks the tree case by case. It checks every
column name it touches with `validateSQLIdentifier` before that name
is placed into the string. It appends every comparison value to the
bindings array, rather than ever placing a value directly into the
text. Values are always safe this way, because SQLite's own
parameter-binding step handles them. Column and table names cannot be
bound as parameters in SQL, which is exactly why they need the
separate identifier check instead.

### SQLiteIdentifierValidator.swift

This file has `validateSQLIdentifier(_:)`. Every write path and the
predicate compiler in this module call this one free function. They
call it before placing any caller-supplied name into a SQL string. The rule
is simple and strict. The first character must be a letter or an
underscore. Every character after that must be a letter, a digit, or
an underscore. This is the safe part of SQLite identifier syntax. The
file's own comment states why this exists as one shared function
rather than several private copies. A name holding a double-quote
character can escape the double-quote mark SQL uses around
identifiers. It can change what the query actually does. Double-
quoting alone is not a strong enough defense on its own. Having one
seam means the defense cannot quietly drift out of sync between the
several call sites that need it.

### SQLiteObserver.swift

This file has `SQLiteObserverRegistry`, an `actor` holding the
subscriber lists for both row-change and blob-change notices on one
SQLite estate. It also has `SQLiteObserver`, the public
`StorageObserver` conformance built on top of it. The file's own
comment explains a real limit of SQLite's native
`sqlite3_update_hook`. It fires for every table, including the
internal blob table. It only reports which operation happened on
which table and row ID. It never carries the actual column values.
Because a blob-change notice needs to carry the bytes that were
written, blob notices cannot be rebuilt from the hook at all. Instead,
`SQLiteBackend`'s `putBlob`/`deleteBlob` methods call this registry's
`notifyBlob(_:)` directly, at the exact point where the bytes are
already in hand.

### KeychainKeyStore.swift

This file has `KeychainKeyStore`. This is the Apple-platform source
of the whole-database encryption key, Mode 3. It is the Apple match
to the Rust port's per-estate key file on disk. One 256-bit key is stored per estate in the system Keychain. Its
account name is worked out in a fixed way from the estate's own file
path.
`estateAccount(for:)` makes the path uniform and hashes it with
SHA-256. Any process told the same estate's file path works out the
same account name this way. The main app and a managed background
server it starts are one such pair of processes. Both load the same
key with no separate key-sharing step needed. `loadOrCreateKey()` reads an
existing key if one is present. If not, it makes a fresh, randomly
generated key and stores it. It handles the case where two callers try to make the key at the
same time. The loser simply re-reads what the winner wrote. `deleteKey()` gets rid of an estate's
key for good when the estate itself is deleted. It is documented as
safe to call twice: calling it on an estate whose key is already gone
counts as success, not an error. The stored key's Keychain access
level, `afterFirstUnlockThisDeviceOnly`, is picked for a specific
reason. A background process can still read the key after the very first
unlock following a device restart. The key never leaves the device.
It never syncs to iCloud.

## Target: PersistenceKitPostgreSQL

### PostgreSQLStorage.swift

This file has `PostgreSQLStorage`, the `Storage` conformance for the
server backend. It also has the `PostgreSQLBackend` actor. That actor
holds the schema and drives migrations, transactions, and health
checks against a connection pool. The most distinct design choice
here is estate isolation. Rather than one PostgreSQL database per
estate, every estate gets its own PostgreSQL schema. A schema, here,
is a namespace within one shared database. Each one is named
`pk_<estate UUID with dashes removed>`. Every pooled connection for
that estate pins its `search_path` to that schema. `public` stays on
the path too, so any shared extension still resolves. This is the
PostgreSQL match to SQLite's one-file-per-estate model. Many estates
can share one database server without their tables ever colliding.

Schema-version bookkeeping uses a simple key-value table,
`_storagekit_meta`, rather than a dedicated migrations table like
SQLite's. A per-kit version is stored under the mixed key
`"schema_version:<kitID>"`. A running highest version is kept under
the plain key `"schema_version"`. This way, the no-argument
`currentSchemaVersion()` still returns something useful even when
several kits share one estate. `storageStats(now:)` gives
`StorageIntrospection` for PostgreSQL by asking three different
system views. It queries `pg_database_size`. It queries
`pg_stat_database`, for buffer-cache hit ratio and transaction
counters. It queries `pg_locks` joined against `pg_database`, for lock
contention. The file documents the exact formula it uses to turn each
view's raw counters into `StorageStats` fields.

### PostgreSQLPool.swift

This file has `PostgreSQLPool`, a fixed-size connection pool built as
an `actor`. Its bookkeeping never needs a separate lock this way.
That bookkeeping is open connections, in-use count, and waiters.
`acquire()` returns
an idle connection right away if one exists. It opens a brand-new one
if the pool has not yet reached its set size. Otherwise, it pauses the
caller on a `CheckedContinuation` until either a connection frees up
or a timeout passes. At that point, the waiter wakes back up with
`StorageError.poolExhausted`. Every freshly-opened connection is
pinned right away to the estate's own PostgreSQL schema. This happens
through `CREATE SCHEMA IF NOT EXISTS`, followed by `SET search_path`.
The connection is closed rather than kept if that two-step setup fails
partway through, so a half-set-up connection is never handed back to
a caller.

`parseTLSMode(host:dsnSSLMode:)` now weighs two sources together. One
is the DSN's own `sslmode=` query parameter, read out by the private
`sslMode(from:)` helper. The other is the `ARIA_MCP_POSTGRES_TLS`
environment variable, as before. The pure function
`effectiveTLSDecision(dsnSSLMode:envValue:)` picks between them. It
ranks each value with the private `TLSModeRank` enum, from `disable`
at the weak end up through `verifyFull`. The effective mode is always
the stronger of the two ranks. The environment variable may raise the
required security level above what the DSN asked for. It can never
lower that level. This closes a real gap in the earlier version. A
DSN of `?sslmode=require` used to be ignored outright, so a connection
could still open in plain text. An unrecognized DSN value now ranks
as `unknownRequireTLS`, above every named mode. A typo in the DSN
therefore fails closed to full TLS, rather than dropping quietly to
plain text. An absent DSN mode paired with an absent environment
variable still defaults to `prefer`, matching `libpq`'s own default.
Even a loopback connection gets this same default rather than
`disable`. A caller who truly wants plain text on loopback has to say
so on purpose, rather than getting it by accident.

### PostgreSQLConnection.swift

This file has free functions that bridge between `PersistenceKit`'s
`TypedValue` and the `postgres-nio` client library's own binding and
decoding types. `executeSimple(_:logger:)` and
`executeParameterized(_:bindings:logger:)` are small `PostgresConnection`
extension methods. They wrap every underlying `postgres-nio` error in
`StorageError.backendError`, so a caller never has to catch a
`postgres-nio`-specific error type directly. `makeBindings(_:)` turns
an array of `TypedValue` into the positional `$1, $2, ...` bindings
PostgreSQL's parameterized-query protocol expects. It handles each
`TypedValue` case's own wire shape. Take `.fingerprint`, for example.
It is written out as thirty-two raw bytes in a fixed block order. This
makes it round-trip exactly.

`decodeRow(_:columns:)` and the private `decodeCell(_:type:)` do the
reverse. Given a returned `PostgresRow` and the caller's expected
column types, they decode each named cell into the matching
`TypedValue` case. If a cell fails to decode as its declared type,
they fall back to `.null` rather than throwing. This is a more open
stance than the SQLite backend's `corruptStoredValue` throw. It
reflects that PostgreSQL's own wire protocol already enforces column
types at a lower level than SQLite's flexible column affinity does.

### PostgreSQLPredicateCompiler.swift

This file has the PostgreSQL match to `SQLitePredicateCompiler`: the
same `StoragePredicate` tree compiled to PostgreSQL's own SQL dialect.
It uses `$1, $2, ...` numbered spots instead of SQLite's `?`
placeholders. The case-by-case translation is otherwise the same
shape as the SQLite compiler. Every column name is checked with
`validatePSQLIdentifier` before it is placed into the string. Every
comparison value becomes a bound parameter rather than inline text.
One PostgreSQL-only detail stands out. Two bitmask predicate cases
work this way: `bitmaskAll` and `bitwiseEq`. Both must point at two
already-appended bindings by their final number: `bindings.count - 1`
and `bindings.count`. This is because PostgreSQL's numbered spots are
numbered by the order they were bound, unlike SQLite's plain `?`
placeholders, which carry no number at all.

### PostgreSQLSchema.swift

This file has `PostgreSQLSchemaEmitter`, the PostgreSQL match to
`SQLiteSchema`: pure functions that turn a `SchemaDeclaration` into
PostgreSQL DDL. `typeSQL(_:)` maps each `ColumnType` to its own
PostgreSQL native type. `.uuid` maps to a real `UUID` column, unlike
SQLite's `TEXT` fallback. `.timestamp` maps to `TIMESTAMPTZ`.
`.json` maps to `JSONB`, PostgreSQL's own binary, searchable JSON
type. PostgreSQL has no `CREATE TRIGGER IF NOT EXISTS`.
`appendOnlyTriggerStatements(_:)` gets the same repeat-safe behavior
SQLite gets for free. It first drops any existing trigger of the same
name, then creates it fresh. One shared `appendOnlyFunctionSQL`
trigger function backs every append-only table in the schema, rather
than one function generated per table. It is created with `CREATE OR
REPLACE`, so running schema setup again never fails. It raises an
error naming the table at fault, through PostgreSQL's `TG_TABLE_NAME`
variable.

### PostgreSQLStores.swift

This file has three concrete conformances for the PostgreSQL
backend: `PostgreSQLRowStore`, `PostgreSQLBlobStore`, and
`PostgreSQLAuditLog`. These match `RowStore`, `BlobStore`, and
`AuditLog`. Each one can run
either against a pooled connection grabbed for a single call, or
against a `PostgreSQLTransactionContext`'s already-open connection
when called from inside a transaction block. Every write method here
checks SQL names with `validatePSQLIdentifier`, exactly as the SQLite
backend does, before it puts together its SQL string.

`PostgreSQLRowStore.query(...)` reads the schema's declared column list
for the target table, rather than issuing a bare `SELECT *`. This is
so a generated column's PostgreSQL-computed value is decoded with the
right `ColumnType`. It also runs every returned row's values through
`decryptedForRead` before wrapping them in a `StorageRow`.
`update(...)` and `delete(...)` both compile their predicate starting
at the parameter spot right after the last `SET`-clause binding. They
use the private `renderPredicate(_:startIndex:bindings:)` helper. This
helper compiles the predicate as usual. It then renumbers every `$N`
placeholder in reverse order, high numbers first. It does this on
purpose, to avoid `$10` being wrongly rewritten by a simple forward
swap of `$1`.
`PostgreSQLBlobStore` and `PostgreSQLAuditLog` each lazily build their
own backing table on first use, through `CREATE TABLE IF NOT EXISTS`.
The tables are `_storagekit_blobs` and `_storagekit_audit`. Neither one needs the
caller's own schema to know about them. This is the same design
`PersistenceKit` uses for SQLite's own internal tables.
`decodeAuditEvent(_:)` decodes the audit row's required fields in a
strict way, so a decode failure there passes on as a thrown error. It
treats optional before-state fields in a looser way, with `try?`.
`NULL` is the expected, valid value for "no prior state," and that
value is always safe there.

### PostgreSQLIdentifierValidator.swift

This file has `validatePSQLIdentifier(_:)`, the PostgreSQL-module
match to `SQLiteIdentifierValidator.swift`'s `validateSQLIdentifier`.
It enforces the exact same rule. The first character must be a
letter or underscore. Every later character must be a letter, a
digit, or an underscore. The file's own comment names this as one of three
copies that enforce this same rule on their own. The other two are
the SQLite module's copy and the Rust port's `validate_sql_identifier`
function. Three copies exist by need. Swift extensions cannot share a
free function across two separate targets without a shared dependency
neither module otherwise needs. Each copy is still the single seam
within its own module, so the rule cannot quietly drift within a
module.

## Target: PersistenceKitReplication

### ReplicationTypes.swift

This file has the two public types every other file in this module
builds on. `ReplicationCursor` is the opaque watermark a caller stores
and passes back into a later incremental sync. It carries the highest HLC seen across every copied row and audit
event. It also carries counts of rows, audit events, and blobs
written during that run. `ReplicationError` is
the closed error type for replication on its own. `schemaMismatch`
fires when source and destination disagree on schema version or kit
ID. Replication on purpose refuses to auto-migrate either side.
`storageFailure` wraps an underlying `StorageError` surfaced during
the copy, re-wrapped as a plain string. This lets `ReplicationError`
itself stay `Equatable`, which a raw `Error` cannot always guarantee.

### StorageReplicator.swift

This file has `StorageReplicator`, the full-snapshot replication
tool. `replicate(from:to:schema:)` is the core function. Two
direction-named helpers sit on top of it: `flush(from:into:schema:)`
moves in-memory data into durable storage, and `hydrate(into:from:schema:)`
moves durable storage into a fresh in-memory copy. "Full snapshot"
means exactly what it says. Every run copies everything, no matter what changed since the last
run. This means every row in every schema-declared table, every audit
event, and every blob. The function still stays even on
repeat runs, though. Every row upsert uses the table's own primary
key as its conflict column, not the random UUID a fresh `RowHandle`
would carry. Running the same flush twice against an unchanged source
updates existing target rows in place, rather than ever
inserting a second copy.

The work runs in three ordered steps. Step one is a schema gate.
Source and destination must report the exact same per-kit schema
version, or the function throws `ReplicationError.schemaMismatch`
right away, rather than trying any copy at all. Step two reads the
entire source into memory: every table's rows, every audit event,
and every blob. Any generated columns are filtered out of each row
first, because writing a value into a column the target computes
on its own would be turned down by both SQL backends. All of this
goes into one `Sendable` in-memory payload, before the target
transaction ever opens. The
file's comment explains this order. It exists so the target is
never left holding a long-lived serializable transaction open while
slow source work runs underneath it. Step three writes that entire
payload into the target inside one plain transaction. A
crash or a thrown error partway through therefore leaves the
target exactly as it was before the flush began. A blob-deletion
pass at the end of step three removes any target blob key absent
from the source snapshot. This closes a gap the file names by its fix
ID, SECFIX-WS2-PK F5. Say this pass did not exist. A blob deleted at
the source would linger forever in the target, after every later
full-snapshot flush. A copy that only ever adds rows never notices
that something is missing.

### IncrementalReplicationSession.swift

This file has `IncrementalReplicationSession`, the observer-driven
alternative to a full snapshot. Instead of copying everything on every
run, it watches a source's live change stream. On demand, it syncs
only the rows and blobs that truly changed since the last sync. The
file's opening comment lays out the design choice in plain terms.
A durable dirty-row table, written on every observed change, would
tie this backend-agnostic module to one particular storage schema.
The session avoids that. It gathers dirty IDs purely in memory
instead, inside two actors: `DirtySet` for rows and `BlobDirtySet`
for blobs. It re-reads each dirty row's current state from the source
at sync time. It does not trust whatever the original change notice
said. The row may have changed again since it was first marked dirty.
If the process restarts, the in-memory dirty set is lost. The fix for
that, as the file notes, is simple. Fall back to a full snapshot. That
always works as a safe stand-in for a lost incremental history.

`DirtyKey` names one dirty row by its table name and its
primary-key values, packed into a sortable string. Draining the dirty
set therefore always produces the same order of work across repeated
runs. This fixed order is what makes two different processes
independently syncing the same dirty set produce an identical, and
so safely repeatable, run of target writes.
`DirtySet.accumulate(_:)` pulls the primary-key columns out of an
observed `TableChange`'s values. If a conforming-but-buggy backend
ever emits a change missing one of those columns, the function logs
it and skips it, rather than crashing. `BlobDirtySet` tracks the same idea for
blobs. A blob change notice already carries its own payload bytes, so
`BlobDirtySet` never needs to re-read anything from the source at
sync time. A `put` replaces an earlier `put` for the same key. A
`delete` replaces a `put`. The last write always wins.

`sync(from:to:fromCursor:)` is the operation a caller actually calls.
It repeats the same schema gate `StorageReplicator` uses. It drains
both dirty sets. It re-scans each dirty row from the source. A
now-missing row turns into a target delete rather than an upsert.
This happens because the row was deleted at the source between the
original change notice and this re-scan. It fetches only the audit events newer than
the previous cursor's watermark. It commits everything inside one
plain target transaction, all in one go, the same fail-loud
shape `StorageReplicator` uses for a full snapshot. The
retry-keeping rule is the one piece unique to the incremental path.
Say anything fails after the dirty sets have already been drained. The
drained keys and blob operations go back into the sets. This restore
is awaited right there in the `catch` block. It is never handed off
to a detached background task. A fire-and-forget restore could race a
caller's immediate retry. It could quietly lose the very keys it was
trying to keep safe. A later retry then tries again on exactly the
rows and blobs the failed run did not finish.

## Rust Port and Conformance

The `rust/` folder holds a second build of `PersistenceKit`'s design,
for use outside the Swift and Apple world. It has the same closed `StoragePredicate` algebra and the same
`TypedValue` case set. It also has the same trait surface: `Storage`,
`RowStore`, `BlobStore`, `AuditLog`, and `StorageObserver`. It has all
three backends too:
`InMemoryStorage`, `SqliteStorage`, and `PostgresStorage`. It adds four things on top of these: the caching decorator, the
hashing decorator, the encryption seam, and both replication modes.
The Rust traits are plain. They use `Result<T, StorageError>` rather
than `async`. This is because the Rust backends do no real async
work of their own. The port's own `README.md` notes that Swift's `async` need comes
from Swift actors in particular. A future backend that does need
async work of its own could wrap its own runtime without changing the
trait shape.

Unlike LatticeLib's two legs, the Swift and Rust sides of
`PersistenceKit` are not gated by a shared, byte-for-byte conformance-
fixture set. `PersistenceKit`'s cross-platform contract is a shared
protocol shape and a shared wire format: the same `TypedValue` cases,
the same predicate tree, the same identifier-checking rule. It is not
a promise that the same input always produces bit-identical output on
both legs. Each side runs its own test suite. The Rust SQLite and
PostgreSQL backends each run a ten-test conformance set covering
schema, rows, predicates, blobs, audit, generated columns,
transactions, append-only rules, and health checks. These tests run
straight against the Rust trait code, not cross-checked against the
Swift package's own test output.
