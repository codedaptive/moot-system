---
doc: AGENT_MAP
package: PersistenceKit
repo: moot-system
authored_commit: f1c1f3bf8dafd26faf5df26c2ddf2ea909e2df18
authored_date: 2026-07-23
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
    blob: 1a295d4514389418cb3b59282894718f7168f29b
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
    blob: fb296fd7f23bf049cc4ee9dfcaedd145fe1daeb5
  - path: Sources/PersistenceKitInMemory/PredicateEvaluator.swift
    blob: ae063f71755c1d2b958fb804a3e898db3c53965b
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLConnection.swift
    blob: e4d687ebae5366425d7a43e6d453cb55c4f6d025
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLIdentifierValidator.swift
    blob: 120cf0c1576a7db45d79bf6865e2f15cff09a5ac
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLPool.swift
    blob: 98dc0350228421361e43d1a363cb81989f790c91
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLPredicateCompiler.swift
    blob: 6f0791e0372b09cf2605bae4cf149e48bbba2834
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLSchema.swift
    blob: f165f0877b96c87e2f7de46012f8f8acb3c03cc8
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLStorage.swift
    blob: edbc418ea47d03ace0af25a870f095c33462e508
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
    blob: b7155d602f43d0cff3e3c0d6fba74a184eda0378
  - path: Sources/PersistenceKitSQLite/SQLiteStorage.swift
    blob: 0b7f1293af172d471057a7298c40a1ca24421691
  - path: Sources/PersistenceKitSQLite/SQLiteStores.swift
    blob: 76499ffb70d979f0d13e8cf9e32bc38ff28ffdb5
  - path: Sources/PersistenceKit/DatasetStore.swift
    blob: 9bb1962726f05bcaecc060a44f205b03a37c32df
  - path: Sources/PersistenceKitInMemory/InMemoryDatasetStore.swift
    blob: 91fbd32c86c8ba26f2da79c130505e94bd7a31a0
  - path: Sources/PersistenceKitPostgreSQL/PostgreSQLDatasetStore.swift
    blob: e5c1f22b481d0af5eb02527d54030f70b74384c9
  - path: Sources/PersistenceKitSQLite/SQLiteDatasetStore.swift
    blob: 8d200d1b465132aa9625492f4f8a403b9fd3e1d2
---

# AGENT_MAP: PersistenceKit

PURPOSE: storage abstraction layer for MOOTx01 estates. One protocol
surface (Storage = RowStore+BlobStore+AuditLog+StorageObserver), three
interchangeable backends (SQLite/PostgreSQL/InMemory), decorators
(caching, hash-on-write) and cross-cutting toolkits (row encryption,
erasure ledger/overlay, snapshot registry+GC pin, telemetry) built on the
core protocol, plus a full-estate replication module. Owns NO vector
search (VectorKit owns k-NN; PersistenceKit only guarantees the
ACCOMMODATION contract: storage round-trip for vector payloads).

DEPS: imports SubstrateTypes (HLC, Fingerprint256, AuditEvent,
AsOfCoordinate, ContentHash: defined upstream, NOT in this package),
IntellectusLib (telemetry, zero-cost when disabled), CryptoKit
(AES-GCM), Security/Keychain (Apple key store, SQLite target only),
postgres-nio + NIOSSL (PostgreSQL target only), vendored SQLCipher C
target (SQLite target only). Imported by: every kit that persists estate
data (LocusKit and others up the graph); kits never import a backend
target directly except at estate-construction time. Rust port in
`rust/` mirrors the trait surface and backend set but is NOT
conformance-fixture-gated against the Swift side (no shared byte-
identical corpus): the cross-language contract is the protocol shape
and wire format only.


CURRENT TRUE-UP:
- v1.0.24: `EstateConfiguration.residencyHint` defaults to `.diskBacked`; `.ramResident` preserves the old heap cache path. SQLite uses mmap and 0600 files. Transactions invalidate present cache entries. RowCrypto allows empty content for expunge. Audit `reason` columns migrate on open.

ENTRY POINTS (most callers need only these):
- Storage.swift:16 `protocol Storage`: conform once per backend; exposes `.rowStore`/`.blobStore`/`.auditLog`/`.observer`
- Storage.swift:29 `Storage.open(schema:)`: bring backend up to declared schema
- Storage.swift:36 `Storage.transaction(isolation:_:)`: atomic multi-op block; ext. default isolation = .readCommitted
- RowStore.swift:33 `RowStore.insert/upsert/update/delete/query/count`: core row I/O
- EstateConfiguration.swift:8 `struct EstateConfiguration`: one value opens one Storage instance
- StorageReplicator.swift:89 `StorageReplicator.replicate(from:to:schema:)`: full-snapshot estate copy

## Symbol Table

- DatasetStore.swift `protocol DatasetStore`: create, append, projected query, column stats, and drop for UUID-backed user tables.
- DatasetStore.swift `DatasetSchema` / `DatasetIndexDeclaration`: validated schema and single-column index declarations.
- DatasetStore.swift `validateDatasetColumnIdentifier`: one portable identifier gate for every backend.
- SQLiteDatasetStore.swift / PostgreSQLDatasetStore.swift / InMemoryDatasetStore.swift: backend implementations with byte-order text parity.
- Storage.swift `datasetStore`: throwing shaped accessor; unsupported third-party backends fail with `featureGated`.

### Core protocols: Storage.swift, RowStore.swift, BlobStore.swift, AuditLog.swift, StorageObserver.swift, StorageIntrospection.swift, Transaction.swift
- Storage.swift:16 `protocol Storage: Sendable`: rowStore/blobStore/auditLog/observer + open/close/transaction/migrate/currentSchemaVersion(for:)
- Storage.swift:58 `extension Storage.transaction(_:)`: default isolation .readCommitted
- RowStore.swift:8 `typealias RowKey = UUID`
- RowStore.swift:10 `struct StorageRow`: [String:TypedValue] wrapper, subscript by column name
- RowStore.swift:22 `struct RowHandle: Hashable`: (table, key)
- RowStore.swift:32 `protocol RowStore: Sendable`: insert/upsert/update/delete/query/count/querySkipCorrupt/query(...columns:)/begin·commit·rollbackTransaction (all true requirements, not ext defaults: needed for dynamic dispatch through `any RowStore`)
- RowStore.swift:72 `querySkipCorrupt(...)`: best-effort corpus scan, skips+counts StorageError.corruptStoredValue rows; NEVER use for point lookups
- RowStore.swift:96 `query(...columns:)`: column projection = no-blob read path; nil columns = full read (superset, always correct)
- RowStore.swift:194/214/236 `query/querySkipCorrupt(...asOf:)`: .present passthrough; .asOf(_) throws StorageError.featureGated("asOfQuery") until NT-L4+NT-P3 both merge
- RowStore.swift:260-266 ext defaults: beginTransaction/commitTransaction/rollbackTransaction: no-op (correct for backends w/o multi-stmt txn)
- BlobStore.swift:8 `typealias BlobKey = String`
- BlobStore.swift:10 `protocol BlobStore: Sendable`: put/get/delete/exists/size/listKeys; listKeys() order UNSPECIFIED (caller sorts)
- AuditLog.swift:23 `protocol AuditLog: Sendable`: append/appendBatch (idempotent on (eventID,hlc))/iterate(after:rowID:limit:)/eventsForRow/count
- StorageObserver.swift:26 `enum StorageEvent`: insert|update|delete
- StorageObserver.swift:32 `struct TableChange`: table/event/rowKey/values/hlc
- StorageObserver.swift:60 `enum BlobEvent`: put|delete
- StorageObserver.swift:73 `struct BlobChange`: key/event/bytes (bytes non-nil only on .put; last-write-wins on same key)
- StorageObserver.swift:111 `struct DirtyChainEvent`: changedRowId/parentNodeId/grandparentNodeId/contentHash/table: hash-on-write payload (NT-P2)
- StorageObserver.swift:140 `protocol StorageObserver: Sendable`: observe(table:events:)/observeBlobs()/observeDirtyChain(); delivery AT-LEAST-ONCE, order preserved PER-TABLE only
- StorageObserver.swift:168 ext default `observeDirtyChain()`: immediately-finished stream (back-compat for pre-hash-on-write observers)
- StorageIntrospection.swift:39 `struct StorageStats`: superset of fields across 3 backends; nil = "not measured" (see file's field/backend table); capturedAt is caller-injected (NEVER Date() inside engine)
- StorageIntrospection.swift:209 `protocol StorageIntrospection`: separate from Storage (additive capability); probe via `as? StorageIntrospection`
- Transaction.swift:9 `enum IsolationLevel`: readCommitted|repeatableRead|serializable
- Transaction.swift:15 `protocol StorageTransaction: Sendable`: rowStore/blobStore/auditLog (no nested txns, no savepoints in v1.0)

### Value/type algebra: TypedValue.swift, Column.swift, Predicate.swift, Schema.swift, GeneratedColumn.swift, StorageError.swift
- TypedValue.swift:25 `enum TypedValue: Sendable, Hashable`: CLOSED 13-case wire format (null/bool/int/bitmap/float/text/blob/uuid/timestamp/json/hlc/fingerprint/array); new case = update every backend, deliberate cost
- Column.swift:9 `struct Column: Comparable`: (table,name); ordered by table then name for stable test fixtures
- Column.swift:24 `enum ColumnType`: uuid|bitmap|text|timestamp|float|int|bool|blob|json|hlc|fingerprint
- Predicate.swift:11 `indirect enum StoragePredicate`: CLOSED; logical(and/or/not/isTrue/isFalse) + comparison(eq/neq/lt/lte/gt/gte/isNull/isNotNull/in/like) + bitmap(bitmaskAll/Any/None/bitwiseEq, Int64 cols only)
- Predicate.swift:41 `StoragePredicate.all(_:)`: AND-combine w/ short-circuit: empty→isTrue, contains isFalse→isFalse
- Predicate.swift:55 `StoragePredicate.any(_:)`: OR-combine w/ short-circuit: empty→isFalse, contains isTrue→isTrue
- Predicate.swift:74 `struct OrderClause`: column + OrderDirection (default .ascending)
- Schema.swift:9 `struct SchemaDeclaration`: kitID/version/tables/indices/migrations
- Schema.swift:31 `struct TableDeclaration`: name/columns/primaryKey/uniqueConstraints/generatedColumns/appendOnly/hashable
- Schema.swift:82 `enum ColumnRole`: createdHlc|tombstonedHlc (as-of temporal filter tagging, ADR-017 §15)
- Schema.swift:90 `struct ColumnDeclaration`: name/type/nullable/defaultValue/role
- Schema.swift:128 `struct Migration`: fromVersion/toVersion/[SchemaOperation]
- Schema.swift:140 `enum SchemaOperation`: createTable/dropTable/addColumn/dropColumn/renameColumn/addIndex/dropIndex/custom(sqlite:postgresql:)
- Schema.swift:153-209 `ColumnDeclaration` static convenience ctors (.uuid/.bitmap/.text/.timestamp/.int/.float/.bool/.blob/.json/.hlc/.createdHlc/.tombstonedHlc/.fingerprint)
- Schema.swift:214-230 `TableDeclaration.createdHlcColumn/tombstonedHlcColumn/supportsAsOfFilter`: derive as-of eligibility from column roles
- GeneratedColumn.swift:43 `struct GeneratedColumn`: name/type/expression; ALWAYS STORED (PG has no VIRTUAL)
- GeneratedColumn.swift:63 `indirect enum GeneratedExpression`: column/literal/bitAnd/bitOr/bitXor/shiftRight/shiftLeft/equal/notEqual; evaluates to Int64 (bool=0/1)
- GeneratedColumn.swift:91 `renderSQL()`: shared SQLite+PG renderer; XOR emitted as (a|b)-(a&b) (SQLite lacks native XOR op)
- GeneratedColumn.swift:122 `evaluate(_:)`: InMemory direct evaluation against row dict
- GeneratedColumn.swift:150 `integerValue(_:)`: extract Int64 from int-family TypedValue; non-integer/absent → 0 (InMemory sentinel)
- StorageError.swift:5 `enum StorageError: Error, Equatable`: CLOSED; notable cases: :22 corruptStoredValue (NEVER fabricate a default: throw), :28 invalidConfiguration (fail-closed on platform-invalid config), :37 featureGated(feature:) (as-of query gate), :44 invalidIdentifier(name:) (SQL identifier injection guard, SECFIX-WS2-PK)

### Estate configuration: EstateConfiguration.swift, EstateCacheConfig.swift, EncryptionMode.swift, NovelTokenTaggerChoice.swift
- EstateConfiguration.swift:8 `struct EstateConfiguration`: estateID/backend/encryptionConfig(default .plaintext)/cacheConfig(default .disabled)/novelTokenTagger(default .hmm)
- EstateConfiguration.swift:45 `enum BackendConfiguration`: .sqlite(url:busyTimeout:) | .postgresql(connectionString:poolSize:connectionTimeout:idleTimeout:) | .inMemory
- EstateConfiguration.swift:108 `queueSibling(filename:)`: derives sibling EstateConfiguration for per-estate queue DB; .sqlite→sibling file `<stem>.<filename>`; .inMemory→sibling inMemory; .postgresql→ throws featureGated (deferred, ADR-021)
- EstateConfiguration.swift:166 `deriveQueueSiblingID(parentID:filename:)` (private): deterministic XOR-fold, NO UUID() call, same parent+filename ⇒ same sibling ID always
- EstateCacheConfig.swift:23 `struct EstateCacheConfig`: enabled/ceilingBytes(clamped ≥0)/sensitivityThreshold(clamped ≤2: Secret=3 NEVER cacheable)
- EstateCacheConfig.swift:53 `EstateCacheConfig.disabled`: zero-change default, cache off
- EncryptionMode.swift:20 `enum EncryptionMode`: .plaintext (Mode1) | .rowEncryption (Mode2, per-row AES-GCM) | .fullDatabase (Mode3, SQLCipher whole-file); Mode4 (DB+threshold/FedRAMP) DELIBERATELY ABSENT: not a build capability
- EncryptionMode.swift:44 `struct EstateEncryptionConfig`: mode/keyIdentifier/`package`-scoped key (SymmetricKey?, never public, never logged)
- EncryptionMode.swift:68 `init(_ mode:)`: mints fresh 256-bit key + UUID keyID for encrypting modes; nil/nil for .plaintext
- EncryptionMode.swift:88 `fullDatabase(key:)`: builds Mode3 config from caller-supplied 256-bit key (Keychain-sourced)
- EncryptionMode.swift:99 `usesRowCrypto`: true ONLY for .rowEncryption (Mode3 has no per-row seam: whole file is ciphertext)
- EncryptionMode.swift:106 `fullDatabaseKeyHex`: lowercase hex for `PRAGMA key`; NEVER logged
- NovelTokenTaggerChoice.swift:43 `enum NovelTokenTaggerChoice: Sendable, Hashable, Codable`: .hmm (default, cross-platform-deterministic, federation-safe) | .nlTagger (Apple-only, non-deterministic across OS versions, federation-INCOMPATIBLE w/o re-tag: enforcement deferred to v1.1); FIXED AT ESTATE CREATION, no change-after-creation in v1.0; mirrors identically-named PersistenceKit-independent enum in LatticeLib: bridge by switch, no shared import
- NovelTokenTaggerChoice.swift:74 `NovelTokenTaggerChoice.default`: .hmm

### Row-level crypto: RowCrypto.swift
- RowCrypto.swift:61 `protocol AeadProvider` (package): swap point for AEAD algorithm; MUST generate fresh random nonce per encrypt call, MUST return [nonce][tag][ciphertext], MUST throw (never return garbage) on auth failure
- RowCrypto.swift:81 `struct CryptoKitAeadProvider: AeadProvider`: default; AES-GCM-256; :87 nonceByteCount=12, :88 tagByteCount=16
- RowCrypto.swift:144 `enum RowCrypto` (package): :151 encrypt(_:key:provider:), :168 decrypt(_:key:provider:); default provider = CryptoKitAeadProvider()
- RowCrypto.swift:191 `encryptedForWrite(_:config:provider:)` (package): no-op unless usesRowCrypto + "content" column present as .text; encrypts + stamps keyID
- RowCrypto.swift:220 `decryptedForRead(_:config:provider:)` (package): no-op unless keyID present AND matches config.keyIdentifier (mismatched keyID ⇒ pass through as ciphertext, NEVER attempt decrypt under wrong key)
- RowCrypto.swift:249 `assertContentKeyIDInvariant(_:table:config:)` (package): structural guard (FUP-D/E-1): throws constraintViolation if encrypting estate has .text content with empty/absent keyID (seam didn't run)

### Caching + hash-on-write decorators: CachingRowStore.swift, CacheInvalidator.swift, HashingRowStore.swift
- CachingRowStore.swift:46 `typealias ParentChainProvider = @Sendable (String, RowKey) -> [RowHandle]`: kit-supplied Merkle ancestor lookup
- CachingRowStore.swift:52 `final class CachingRowStore: RowStore, Sendable`: LRU hot-tier decorator; TRANSPARENCY GUARANTEE: results always identical to unwrapped backing store
- CachingRowStore.swift:164 `query(...asOf:)`: cache key = (RowHandle, AsOfCoordinate); .present entries evicted on write, .asOf(hlc) entries NEVER evicted (immutable, GC-pinned)
- CachingRowStore.swift:218 `invalidate(table:key:)`: external-write invalidation hook, called by CacheInvalidator
- CachingRowStore.swift:302 `extractKey(from:)`: cache lookups ONLY feasible for single-key `.eq(_, .uuid)` predicates; all else bypasses cache
- CachingRowStore.swift:334 `actor CacheActor`: owns entries/accessCounter/totalBytes; makes outer class Sendable w/o manual locks
- CachingRowStore.swift:421 `isAdmissible(_:)` (private, in CacheActor): sensitivity gate: reads `provenance` bitmap bits[30:35] scale-gapped (0/16/32/48→ordinal 0-3); ordinal==3(Secret) ALWAYS rejected; absent column→admit; unparseable/unrecognized→REJECT (fail-closed)
- CachingRowStore.swift:482 `evictLRU()` (private): O(n) min-scan by accessOrder; acceptable at cache-sized N
- CacheInvalidator.swift:34 `final class CacheInvalidator: Sendable`: StorageObserver→CachingRowStore.invalidate bridge for external writers
- CacheInvalidator.swift:52 `init(cache:observer:tables:)`: subscriptions registered before first await inside Task.detached; race note: a write issued in the SAME instant as init() may precede subscription (caller should yield if strict ordering required)
- CacheInvalidator.swift:92 `cancel()` / deinit: cancels root task ⇒ propagates to all per-table child tasks
- HashingRowStore.swift:36 `typealias ContentHashProvider = @Sendable (table:rowKey:values:) -> ContentHash`: kit-injected hash fn (PersistenceKit never imports a hash impl)
- HashingRowStore.swift:49 `typealias HashParentChainProvider = @Sendable (table:rowKey:) -> (parentNodeId:grandparentNodeId:)?`
- HashingRowStore.swift:55 `struct HashOnWriteConfig`: hashableTables/hashProvider/parentChainProvider
- HashingRowStore.swift:81 `final class HashingRowStore: RowStore, @unchecked Sendable`: decorator chain position: caller → HashingRowStore → CachingRowStore → backend
- HashingRowStore.swift:295 `augmentWithHashForKnownKey(table:rowKey:mergedValues:)` (private): UPDATE/upsert-as-update path: hashes FULL merged row (not partial SET dict) per SECFIX-WS2-PK F6; strips stale content_hash before hashing so insert vs update paths agree
- HashingRowStore.swift:330 `augmentWithHash(table:values:)` (private): INSERT path; extracts rowKey from "id" column by convention
- HashingRowStore.swift:369 `emitDirtyChain(...)` (private): delivers DirtyChainEvent via injected ObserverRegistryRef (nil = computed but not delivered)

### Erasure: ErasureLedger.swift, ErasureOverlay.swift
- ErasureLedger.swift:15 `struct ErasureLedgerEntry`: drawerId + erasedHlc
- ErasureLedger.swift:28 `enum ErasureLedgerTables`: .ledger = "erasure_ledger"
- ErasureLedger.swift:35 `ErasureLedgerSchema.ledgerTable`: TableDeclaration, appendOnly:true (UPDATE/DELETE throw appendOnlyViolation at storage layer)
- ErasureLedger.swift:53 `ErasureLedgerOps.recordErasure(...)`: throws duplicateKey if drawerId already erased (erase-once contract)
- ErasureLedger.swift:68 `ErasureLedgerOps.isErased(...)`: fast point-lookup, hot path for every erasure-subject read
- ErasureLedger.swift:86 `ErasureLedgerOps.lookupErasure(...)`: full entry incl. erasedHlc
- ErasureOverlay.swift:22 `struct ErasureOverlayConfig`: extractErasureId(row)->String? (nil=not erasure-subject) + contentColumns([String]) supplied by calling kit
- ErasureOverlay.swift:64 `ErasureOverlay.apply(rows:config:rowStore:)`: TWO-PHASE FAIL-CLOSED: phase1=query already ran; phase2=per-row ledger check; ledger-check THROW ⇒ row DROPPED entirely (never shown-when-uncertain)
- ErasureOverlay.swift:96 `nullContentColumns(row:columns:)`: nulls listed columns, preserves skeleton (id/hlc/timestamps)

### Snapshots + GC: SnapshotRegistry.swift, GCPin.swift
- SnapshotRegistry.swift:18 `struct SnapshotId: Hashable`: :24 `.mint()` UUID-backed
- SnapshotRegistry.swift:32 `struct SnapshotRecord`: snapshotId/hlc/label/createdAt
- SnapshotRegistry.swift:47 `struct SnapshotAttestation`: snapshotId/subjectKind/subjectId/merkleRoot/keyVersion?; subject semantics owned by CALLING kit, not PersistenceKit
- SnapshotRegistry.swift:73 `enum SnapshotTables`: .registry="snapshot_registry", .attestations="snapshot_attestations"
- SnapshotRegistry.swift:83/98 `SnapshotSchema.registryTable` / `.attestationsTable`: TableDeclarations
- SnapshotRegistry.swift:130 `SnapshotRegistryOps.createSnapshot(...)`: mints SnapshotId, inserts registry row + N attestation rows
- SnapshotRegistry.swift:161 `.listSnapshots(...)`: HLC ascending
- SnapshotRegistry.swift:177 `.deleteSnapshot(...)`: attestations deleted BEFORE registry row (child-first)
- SnapshotRegistry.swift:201 `.attestations(rowStore:snapshotId:)`: ordered by (subjectKind, subjectId)
- GCPin.swift:22 `GCPin.minimumRetainableHlc(rowStore:)`: MIN(hlc) across all snapshots; nil = nothing pinned = all vacuumable
- GCPin.swift:44 `GCPin.isPinned(rowStore:rowHlc:)`: rowHlc.packed >= minRetainableHlc.packed

### Telemetry + observer stubs: PersistenceKitTelemetry.swift, NoOpObserver.swift
- PersistenceKitTelemetry.swift:81 `reportStorageStats(_:estateID:now:)`: OFF by default; single Atomic<Bool> load+branch when Intellectus.isEnabled==false (~1ns, no stats() call); emits `persistence.db.*` namespace, one metric per non-nil StorageStats field; `now` ALWAYS caller-injected (never Date() inline)
- NoOpObserver.swift:10 `final class NoOpObserver: StorageObserver, Sendable`: immediately-finished streams; used directly by PostgreSQLStorage (no live PG notification channel)

### Backend: PersistenceKitInMemory
- InMemoryStorage.swift:24 `final class InMemoryStorage: Storage, Sendable`: no persistence across process runs; always effectively-serializable isolation (full state snapshot per txn)
- InMemoryStorage.swift:78 `transaction(isolation:_:)`: mutates LIVE actor state directly (NOT a detached-copy-then-replace): replace-on-commit would silently drop concurrent bare inserts landing between snapshot and replace (real incident: 5-10% lost QueueKit sends under burst); rollback restores pre-txn snapshot
- InMemoryStorage.swift:102/110 `beginNotificationBuffering()` / `commitNotifications()`: buffer during txn, flush only on successful commit (SECFIX-WS2-PK F2: rolled-back writes must never reach observers)
- InMemoryStorage.swift:144 `actor InMemoryStateActor`: sole owner of InMemoryState; :462 `materializeGenerated(_:_:)` static: computes GeneratedColumn values identically to SQL backends' STORED columns
- InMemoryStorage.swift:388 `queryRows(...)`: predicate/order/pagination applied to FULL row first, projection applied LAST (matches SQL ORDER-BY-on-unselected-column semantics)
- InMemoryStorage.swift:635 `HLC.packed` (extension): canonical bit-pack: physical<<16 | logical<<4 | node
- InMemoryRowStore.swift:19 `final class InMemoryRowStore: RowStore, Sendable`: thin forward to state actor; column-projection kept for cross-backend StorageRow shape parity, not for a real transfer saving
- InMemoryBlobStore.swift:6 `final class InMemoryBlobStore: BlobStore, Sendable`: forwards to actor blob dict
- InMemoryAuditLog.swift:19 `final class InMemoryAuditLog: AuditLog, Sendable`: forwards to actor; dedup on (eventID,hlc) enforced in actor
- InMemoryObserver.swift:15 `final class ObserverRegistry: @unchecked Sendable`: NSLock-based (NOT actor) so `register` is SYNCHRONOUS: subscription recorded before observe() returns, no race window for immediately-following write; mirrors Rust ObserverHub::subscribe
- InMemoryObserver.swift:104 `notify(_:)`: matching subs snapshotted UNDER lock, yielded OUTSIDE lock (avoids deadlock vs. continuation onTermination which also locks)
- PredicateEvaluator.swift:6 `enum PredicateEvaluator`: in-memory StoragePredicate interpreter (only backend evaluating the tree directly rather than compiling to a query string)
- PredicateEvaluator.swift:61 `likeMatch(_:pattern:)`: SQL LIKE→NSRegularExpression (%→.*, _→.)
- PredicateEvaluator.swift:80 `enum TypedValueComparator`: :81 `compare(_:_:)` shared ordering; .null sorts first; .hlc compared via .packed (single total order)

### Backend: PersistenceKitSQLite
- SQLiteStorage.swift:22 `final class SQLiteStorage: Storage, Sendable`: one connection per estate
- SQLiteStorage.swift:110 `actor SQLiteBackend`: owns connection + inTransaction flag + pendingBlobNotifications (buffered during txn, SECFIX-WS2-PK F3)
- SQLiteStorage.swift:334 `runTransaction(isolation:_:)`: on `inTransaction==true`, WAITS (poll every 25ms, cap 60s) instead of throwing on sight, ordinary contention between two async callers suspended in `block`; wait-expired ⇒ transactionConflict; true self-reentrant nesting still fails this way (never hangs)
- SQLiteStorage.swift:396 `beginTransactionDirect()`: unchanged immediate-throw on `inTransaction==true` (synchronous call site, cannot await a wait)
- SQLiteStorage.swift:429 `insertRow` / :483 `upsertRow` / :521 `updateRows` / :565 `deleteRows` / :593 `queryRows`: ALL validate every table/column identifier via `validateSQLIdentifier` before interpolation (SECFIX-WS2-PK F9); insertRow+queryRows run RowCrypto encrypt/decrypt seam; ALL FIVE (+ queryRowsSkipCorrupt/fetchMatchingRowKeys/countRows/iterateAudit) now call `connection.prepareCached(_:)` not `connection.prepare(_:)` (see SQLiteConnection.swift statement cache below)
- SQLiteStorage.swift:694 `queryRowsSkipCorrupt(...)`: cursor-level per-row skip+log on corruptStoredValue; other errors re-thrown (systemic)
- SQLiteStorage.swift:824 `storageStats(now:)`: PRAGMA page_size/page_count/freelist_count; WAL frame count derived from `-wal` FILE SIZE (NOT PRAGMA wal_checkpoint: can SQLITE_LOCKED even same-actor)
- SQLiteStorage.swift:967 `readColumn(...)`: type-tolerant decode (valid-but-coerced value passes through) vs. parse-failure (unparseable UUID/timestamp TEXT) → THROWS corruptStoredValue, never fabricates
- SQLiteConnection.swift:24 `final class SQLiteConnection: @unchecked Sendable`: thin sqlite3 C API wrapper
- SQLiteConnection.swift:29 `init(url:busyTimeout:keyHex:)`: ORDER MATTERS: symlink-refusal check (CAND-052, lstat semantics) → sqlite3_open_v2 → PRAGMA key (Mode3, MUST be first stmt) → Apple Data Protection → WAL/synchronous/busy_timeout/foreign_keys pragmas
- SQLiteConnection.swift:102 `close()`: destroys every cached statement FIRST (loop over statementCache), THEN sqlite3_close_v2; sqlite3_close_v2 defers the real close while any prepared stmt survives, so skipping this order leaves a zombie connection
- SQLiteConnection.swift:156 `statementCache: [String: SQLiteStatement]` (:157 capacity 128): per-connection cache keyed by SQL text; at-capacity eviction destroys+rebuilds the WHOLE cache (crude, O(1) amortized); mirrors Rust rusqlite prepare_cached
- SQLiteConnection.swift:177 `prepareCached(_:)`: CHECKOUT semantics (mirrors rusqlite): statement REMOVED from cache slot while in use, returned by its own finalize(); a reentrant same-SQL call misses the now-empty slot and prepares fresh instead of clobbering the in-flight statement
- SQLiteConnection.swift:197 `returnToCache(_:)`: called from a cached statement's finalize(); if the slot is already occupied (reentrant duplicate), destroys the returner instead of clobbering the resident
- SQLiteConnection.swift:217 `final class SQLiteStatement`: prepared-stmt wrapper; :225 `isCached` / :228 `cacheKey` set by prepareCached, read by finalize(); :269 `bind(_:at:)` TypedValue→sqlite3_bind_*; :328 `step()`
- SQLiteConnection.swift:237 `finalize()`: on a cached stmt, RETURNS it to the cache reset-for-reuse rather than destroying it; existing `defer { stmt.finalize() }` call sites need no change whether the stmt came from prepare or prepareCached
- SQLiteConnection.swift:253 `resetForReuse()` (sqlite3_reset + sqlite3_clear_bindings, cache-reuse path) / :262 `destroy()` (unconditional sqlite3_finalize, used by cache eviction + close())
- SQLiteConnection.swift:407 `enum ISO8601`: :445 `string(from:)` clamps Date to RFC-3339 range [0001,9999] before formatting (logs warning on clamp, never emits unparseable-back string); :481 `date(from:)` tries :501 `fastParseCanonicalUTC` FIRST (allocation-free, handles the exact canonical shape this kit writes: Merkle rollup re-decode was ~80% of CPU via ICU formatter before this fast path), falls back to ISO8601DateFormatter for anything else
- SQLiteStores.swift:22 `final class SQLiteRowStore: RowStore, Sendable`: forwards to SQLiteBackend; :75 `querySkipCorrupt` overrides protocol default with real cursor-level skip
- SQLiteStores.swift:90/102/119 `SQLiteBlobStore` / `SQLiteAuditLog` / `SQLiteTransaction`: thin forwards
- SQLiteSchema.swift:9 `enum SQLiteSchema`: :12 `nativeType(_:)` ColumnType→SQLite storage class (.uuid/.timestamp→TEXT, .hlc→INTEGER packed, .fingerprint→BLOB); :29 `createTable(_:)`; :68 `appendOnlyTriggers(_:)` emits BEFORE UPDATE/DELETE RAISE(ABORT) trigger pair
- SQLiteSchema.swift:103/132/167 internal tables: `_storagekit_migrations` (kit_id PK) / `_storagekit_audit` (event_id+hlc PK, full-precision physical_time/logical_count/node_id columns alongside lossy packed hlc) / `_storagekit_blobs` (key PK)
- SQLitePredicateCompiler.swift:27 `SQLitePredicateCompiler.compile(_:)`: throws on invalidIdentifier; every column name validated before interpolation (SECFIX-WS2-PK F7)
- SQLiteIdentifierValidator.swift:28 `validateSQLIdentifier(_:)`: `[A-Za-z_][A-Za-z0-9_]*`; SINGLE seam for SQLite module (SQLiteStorage + SQLitePredicateCompiler both call this, no forked copy)
- SQLiteObserver.swift:26 `actor SQLiteObserverRegistry`: row subs via sqlite3_update_hook (op/table/rowid ONLY, no column values); blob subs fed directly by SQLiteBackend.putBlob/deleteBlob call sites (hook cannot carry bytes)
- KeychainKeyStore.swift:36 `struct KeychainKeyStore` (Apple-only, `#if canImport(Security)`): Mode3 key source; :72 `estateAccount(for:)` SHA-256(standardized path)→per-estate Keychain account, so co-processes agree without coordination; :82 `loadOrCreateKey()` idempotent under concurrent first-callers; :111 `deleteKey()` idempotent (missing item = success); access class `kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly` (background-readable post-first-unlock, device-only, no iCloud sync)

### Backend: PersistenceKitPostgreSQL
- PostgreSQLStorage.swift:23 `final class PostgreSQLStorage: Storage, Sendable`: observer = NoOpObserver() (no live PG change-notification impl)
- PostgreSQLStorage.swift:47 estate isolation: search_path pinned to `pk_<estateID no-dashes lowercase>` schema per connection (PG analogue of SQLite one-file-per-estate); `public` stays on path for shared extensions
- PostgreSQLStorage.swift:120 `actor PostgreSQLBackend`: :129 `encryptionConfig` is `nonisolated let` (immutable+Sendable, read sync by row stores for the crypto seam)
- PostgreSQLStorage.swift:206/333 per-kit schema version stored in `_storagekit_meta` under composite key `"schema_version:<kitID>"`; global max under plain key `"schema_version"`
- PostgreSQLStorage.swift:246 `storageStats(now:)`: pg_database_size / pg_stat_database (blks_hit,blks_read,xact_commit,xact_rollback,deadlocks) / pg_locks⋈pg_database (granted=false ⇒ lockContention)
- PostgreSQLPool.swift:12 `actor PostgreSQLPool`: fixed-size; :38 `acquire()` reuse→open-new(if under size)→CheckedContinuation-wait w/ timeout→poolExhausted
- PostgreSQLPool.swift:101 `openConnection()`: every new conn: CREATE SCHEMA IF NOT EXISTS + SET search_path, closes conn on setup failure (never hands back half-configured conn)
- PostgreSQLPool.swift:178 `parseTLSMode(host:dsnSSLMode:)`: effective mode = MAX(DSN `sslmode=` via :219 `sslMode(from:)`, `ARIA_MCP_POSTGRES_TLS` env var) computed by :203 `effectiveTLSDecision(dsnSSLMode:envValue:)` (SECFIX-C-PG-SWIFT-TLS-SSLMODE, parity w/ Rust `postgres_tls::effective_sslmode`); env may RAISE never LOWER the DSN's requirement; absent+absent→prefer, incl. loopback (explicit opt-out required for plaintext)
- PostgreSQLPool.swift:197 `enum TLSDecision`: disable|prefer|require (the 3 PostgresNIO outcomes; extracted for unit-testability w/o a live server/NIOSSLContext)
- PostgreSQLPool.swift:231 `private enum TLSModeRank`: weakest→strongest ranking for libpq sslmode values: disable<allow<prefer<require<verifyCA<verifyFull<unknownRequireTLS; an unrecognized DSN value ranks `unknownRequireTLS` (fail-closed above every named mode, a typo never silently drops to plaintext); previously the DSN's sslmode was ignored entirely, so `?sslmode=require`/`verify-ca`/`verify-full` could still open a plaintext connection
- PostgreSQLConnection.swift:23 `PostgresConnection` ext: :25 `executeSimple` / :33 `executeParameterized` wrap postgres-nio errors as StorageError.backendError
- PostgreSQLConnection.swift:45 `makeBindings(_:)`: TypedValue→PostgresBindings; .fingerprint serialized as 32 raw bytes fixed block order
- PostgreSQLConnection.swift:103/116 `decodeRow(_:columns:)` / `decodeCell(_:type:)`: decode failure → `.null` (NOT a throw: PG wire protocol already enforces column types at a lower level than SQLite affinity)
- PostgreSQLPredicateCompiler.swift:27 `.compile(_:)`: `$1,$2,...` positional params; :89/:102 bitmask cases reference prior bindings by FINAL numeric position (bindings.count-1 / bindings.count)
- PostgreSQLSchema.swift:8 `enum PostgreSQLSchemaEmitter`: :47 `typeSQL(_:)` (.uuid→UUID native, .timestamp→TIMESTAMPTZ, .json→JSONB); :83 `appendOnlyFunctionSQL` ONE shared plpgsql trigger fn (CREATE OR REPLACE, idempotent) for ALL append-only tables; :97 `appendOnlyTriggerStatements` DROP-then-CREATE per table (PG has no CREATE TRIGGER IF NOT EXISTS)
- PostgreSQLStores.swift:25 `final class PostgreSQLRowStore: RowStore, Sendable`: :34 `withConnection` routes to txn.connection if inside a PostgreSQLTransactionContext, else pool.acquire/release
- PostgreSQLStores.swift:240 `renderPredicate(_:startIndex:bindings:)` (private): renumbers `$N` placeholders in REVERSE order (avoid $10 clobbered by naive forward $1 replace)
- PostgreSQLStores.swift:258/352 `PostgreSQLBlobStore` / `PostgreSQLAuditLog`: lazily CREATE TABLE IF NOT EXISTS own backing tables on first use (not in caller's SchemaDeclaration)
- PostgreSQLStores.swift:509 `decodeAuditEvent(_:)`: required fields `try` (throw on failure); optional before-state fields `try?` (NULL = valid "no prior state")
- PostgreSQLIdentifierValidator.swift:26 `validatePSQLIdentifier(_:)`: identical rule to SQLite's validator; independent copy (3 total incl. Rust `validate_sql_identifier`): SECFIX-WS2-PK F7/F9/F10, one seam PER MODULE

### PersistenceKitReplication
- ReplicationTypes.swift:24 `struct ReplicationCursor: Equatable`: hlcWatermark/rowsWritten/auditEventsWritten/blobsWritten
- ReplicationTypes.swift:51 `enum ReplicationError`: schemaMismatch(sourceVersion:destinationVersion:sourceKitID:destinationKitID:) | storageFailure(detail:)
- StorageReplicator.swift:69 `enum StorageReplicator`: :89 `replicate(from:to:schema:)` core; :103 `flush(from:into:schema:)` / :115 `hydrate(into:from:schema:)` direction-named wrappers
- StorageReplicator.swift:125 `replicateFull(...)`: 3 steps: (1) per-kit schema-version GATE (throws schemaMismatch, NO auto-migrate), (2) snapshotSource() reads ALL rows/audit/blobs into Sendable payload BEFORE opening dest txn, (3) one `.serializable` dest transaction does upsert(conflictColumns:=table.primaryKey: NOT RowHandle.key)
- StorageReplicator.swift:216 blob-delete propagation (SECFIX-WS2-PK F5): destination keys ABSENT from source snapshot are deleted; additive-only copy would otherwise leak orphaned blobs forever
- StorageReplicator.swift:256 `snapshotSource(...)`: generated columns FILTERED OUT of each row before staging (destination recomputes); a blob present in listKeys() but absent on get() (TOCTOU) → THROWS storageFailure, never silently dropped
- IncrementalReplicationSession.swift:56 `actor BlobDirtySet`: :61 `accumulate` last-write-wins per key; :67 `drain()` sorted-by-key, atomic clear; :79 `restore(_:)` UNION semantics (does not overwrite newer dirt)
- IncrementalReplicationSession.swift:104 `struct DirtyKey: Comparable`: (table, pkEncoded) sorted string key; ORDER IS LOAD-BEARING: deterministic replay across independent sync runs
- IncrementalReplicationSession.swift:135 `actor DirtySet`: :163 `accumulate(_:)` extracts PK cols from TableChange.values; missing PK col or nil values dict ⇒ LOG+SKIP (never crash); :195 `drain()` sorted; :213 `restore(_:)` union (Set.insert no-op if present)
- IncrementalReplicationSession.swift:242 `final class IncrementalReplicationSession: Sendable`: :283 `start(source:schema:)` subscribes one Task per schema table + one blob Task; :266 deinit cancels all tasks
- IncrementalReplicationSession.swift:361 `sync(from:to:fromCursor:)`: schema-version gate (same as full snapshot) → drain both dirty sets → re-scan each dirty row from SOURCE (never trust the original notification's payload for rows: row may have changed again) → missing row ⇒ destination DELETE, present ⇒ UPSERT → audit events filtered to HLC > fromCursor.hlcWatermark → ONE serializable dest transaction
- IncrementalReplicationSession.swift:396-406 RETRY-PRESERVATION: on ANY error after drain, `dirtySet.restore` + `blobDirtySet.restore` are AWAITED SYNCHRONOUSLY inside the catch: NEVER fire-and-forget/detached: a detached restore could race an immediate caller retry and reproduce the lost-keys bug nondeterministically
- IncrementalReplicationSession.swift:521 `snapshotDirtyRows(...)` (private): blob ops need NO source re-read (change event already carries payload); row ops DO re-scan (predicate = exact PK match, `pkPredicate(for:table:)` at :617)

## INVARIANTS / GOTCHAS

- TypedValue is a CLOSED 13-case enum. Adding a case requires updating every backend (SQLite/PostgreSQL/InMemory/Rust) in the same change: this cost is deliberate, not an oversight.
- StoragePredicate is CLOSED. Backends treat it as opaque data except when compiling; never special-case another backend's predicate shape.
- SQL IDENTIFIER INJECTION GUARD (SECFIX-WS2-PK F1/F7/F9/F10): every caller-supplied table/column name reaching a dynamically-built SQL string is validated against `[A-Za-z_][A-Za-z0-9_]*` FIRST: three independent seams (SQLiteIdentifierValidator.validateSQLIdentifier, PostgreSQLIdentifierValidator.validatePSQLIdentifier, Rust validate_sql_identifier), one per module, never forked within a module. Double-quoting alone is NOT sufficient: a name containing `"` escapes the delimiter.
- corruptStoredValue is THROWN, never papered over. A stored UUID/timestamp string that fails to parse must surface as an error: substituting a fabricated UUID or epoch-0 date is a silent data-identity lie. querySkipCorrupt / queryRowsSkipCorrupt exist specifically so a CORPUS scan (not a point lookup) can skip-and-log instead of aborting wholesale.
- as-of temporal query (.asOf(hlc)) is GATED OFF by default: every default implementation throws StorageError.featureGated("asOfQuery"). Do not implement/ungate a backend override until NT-L4 (lineage-wide expunge) and NT-P3 (erasure overlay) have both merged: ungating early risks resurfacing erased content through a stale snapshot read.
- CachingRowStore TRANSPARENCY GUARANTEE: every read must return exactly what the unwrapped backing store would. Cache changes latency only, NEVER correctness. Sensitivity gate fails CLOSED: unparseable/unrecognized provenance bit patterns are rejected from the cache, never admitted "just in case."
- Sensitivity ordinal 3 (Secret) is NEVER cacheable, enforced twice: EstateCacheConfig clamps sensitivityThreshold to ≤2 at construction, AND CacheActor.isAdmissible re-checks ordinal==3 unconditionally as defense-in-depth.
- Decorator chain order matters: caller → HashingRowStore → CachingRowStore → backend. HashingRowStore must see the row BEFORE caching decides whether to admit it.
- HashingRowStore hashes the FULL committed row on UPDATE/upsert-as-update (pre-read + merge), never the partial SET-columns dict: a partial hash would diverge from what INSERT computes for identical final data (SECFIX-WS2-PK F6).
- RowCrypto: a keyID mismatch on read means "sealed under a key this estate does not hold": pass the row through as still-ciphertext, do NOT attempt decrypt (would only surface as an AES-GCM auth failure). Mode 3 (fullDatabase) makes the per-row seam a permanent no-op (usesRowCrypto==false): the whole file is already ciphertext.
- assertContentKeyIDInvariant is a STRUCTURAL guard, not just documentation: any encrypting-estate write path that reaches a backend with `.text` content and no/empty keyID throws constraintViolation. If you add a new content-bearing write path (a new upsert variant, a migration path), it must run the encryption seam BEFORE this guard sees it, or the guard will correctly reject the write.
- ErasureOverlay is FAIL-CLOSED: a ledger-check failure drops the row from the result entirely rather than showing possibly-erased content. This is NOT the same as failing open: silence (row absence) is the safe failure mode here.
- erasure_ledger table is appendOnly:true, enforced by the STORAGE LAYER (SQLite triggers / PostgreSQL trigger function / InMemory RowStore.update·delete throw): not just by convention in ErasureLedgerOps.
- InMemoryStorage.transaction mutates LIVE actor state (not a detached copy replaced on commit): this is a scar from a real lost-write incident (5-10% of QueueKit sends under burst). Do not "simplify" this back to copy-then-replace.
- Notification buffering (SECFIX-WS2-PK F2/F3) applies during EVERY transaction on EVERY backend (InMemory row+blob, SQLite blob): observers must never see a notification for a write that was later rolled back. Buffer before the block runs, flush only after commit, discard on rollback.
- IncrementalReplicationSession retry-preservation restore MUST be awaited synchronously inside the failure catch block, never dispatched to a detached Task: a detached restore races an immediate caller retry and can reproduce a lost-dirty-keys bug nondeterministically.
- Full-snapshot replication (StorageReplicator) upserts on `table.primaryKey` as the conflict column, NEVER on RowHandle.key (which is a fresh random UUID per call and would insert duplicates on every re-run).
- Replication requires exact per-kit schema-version equality between source and destination (srcVersion == dstVersion == schema.version). No auto-migration, ever: a version mismatch is ReplicationError.schemaMismatch, not a best-effort attempt.
- Generated columns are ALWAYS STORED (never VIRTUAL): PostgreSQL has no VIRTUAL form, and this keeps SQLite/PostgreSQL/InMemory semantically identical. Generated column names are filtered OUT of any row payload staged for upsert during replication (destination recomputes them; writing a value into a GENERATED column errors on both SQL backends).
- HLC.packed truncates physicalTime to 40 bits: lossy for far-future timestamps. SQLite's `_storagekit_audit` table therefore stores physical_time/logical_count/node_id as separate full-precision columns alongside the packed value; decode from those three columns, not from unpacking the packed column, or a cold rebuild's lastHLC can silently disagree with a snapshot's.
- ISO8601 fast-path parser (`fastParseCanonicalUTC`) exists purely for CPU cost: the general ISO8601DateFormatter was measured at ~80% of total CPU during large imports (Merkle rollup re-decodes every row's timestamp on every insert). The fast path recognizes ONLY the exact canonical shape this kit writes and returns nil (triggering formatter fallback) for anything else: never weaken its exactness to "handle more cases," that reintroduces the cost it exists to avoid.
- NovelTokenTaggerChoice is FIXED AT ESTATE CREATION in v1.0: no change-after-creation path exists yet. `.nlTagger` estates cannot safely federate with `.hmm` estates without full re-tagging; this is not yet enforced automatically (v1.1 work): caller discipline is the only guard today.
- PostgreSQL TLS mode (SECFIX-C-PG-SWIFT-TLS-SSLMODE): `parseTLSMode` now reads BOTH the DSN's `sslmode=` query parameter and `ARIA_MCP_POSTGRES_TLS`, taking the stronger of the two via TLSModeRank. Never go back to reading only the env var: an earlier version ignored the DSN's `sslmode` entirely, so `?sslmode=require`/`verify-ca`/`verify-full` could still open a plaintext connection. An unrecognized DSN value ranks above every named mode (fail-closed to require), never as disable.
- SQLiteBackend.runTransaction WAITS on contention (poll 25ms, cap 60s) rather than throwing transactionConflict on sight, because the actor can suspend inside an async `block` and a second concurrent caller landing here is ordinary contention, not a bug. `beginTransactionDirect` (the synchronous explicit-boundary path) is unchanged and still throws immediately: it cannot await a wait.
- SQLiteConnection's per-connection statement cache (`prepareCached`/`returnToCache`, cap 128) is a performance path, not a correctness one: a cached `SQLiteStatement`'s `finalize()` returns it to the cache instead of destroying it, so `close()` MUST destroy every cached statement before calling `sqlite3_close_v2`, or the connection never truly closes (SQLite defers close while any prepared statement survives).
- Pinned/deterministic constants: sensitivityThreshold clamp ≤2, Secret ordinal ==3 always excluded, SQL identifier pattern `[A-Za-z_][A-Za-z0-9_]*`, ciphertext envelope layout [12-byte nonce][16-byte tag][ciphertext], KeychainKeyStore.keyByteCount=32, RFC-3339 round-trip range [0001-01-01, 9999-12-31].
- PersistenceKit imports NO vector-search engine and must never grow one: the ACCOMMODATION contract (vector payload round-trip, bulk hydration, count, delete via the general RowStore/BlobStore surfaces) is the full extent of this package's obligation toward VectorKit's workload (ADR-008).
