---
doc: OVERVIEW
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

# PersistenceKit Overview

## Current Release Notes

PersistenceKit now carries a `residencyHint` on `EstateConfiguration`.
The default is `.diskBacked`.
SQLite and the OS page cache manage large indexes in that mode.
`.ramResident` keeps the old heap-cached behavior for tests and small stores.

The release hardens storage.
SQLite database files are set to owner-only permissions.
Key PRAGMA errors no longer echo key material.
Transaction commits now clear present-read cache entries.

The storage contract now includes `DatasetStore`.
It creates typed user tables with safe column names.
It supports bulk append, filtered query, column stats, and table drop.
SQLite, PostgreSQL, and in-memory backends implement the same surface.
Text comparison uses UTF-8 byte order across every backend and language.

## What This Library Does

PersistenceKit is the storage layer for MOOTx01. MOOTx01 is an on-device
AI memory system. It stores what an AI observes over time. It later
helps the AI recall what it observed. Every fact, drawer, and audit
record a MOOTx01 estate keeps passes through PersistenceKit first. Only
then does the record reach a disk or a database. An estate is one
user's complete memory store in MOOTx01. PersistenceKit does not decide
what a memory means. That job belongs to a higher kit. PersistenceKit
decides how a memory is written, read, and kept safe. It takes over
once another kit hands the memory over as typed rows and bytes.

PersistenceKit gives every higher kit the same core operations. A kit
can insert a row. A kit can fetch a blob. A kit can append an audit
event. A kit can watch a table for changes. It can also manage typed
user datasets. This holds true no matter
which physical engine sits underneath. The engine can be SQLite on a
phone. It can be PostgreSQL on a shared server. It can also be a plain
in-memory table for a unit test. The calling kit writes the same code
in every case.

## The Problem It Solves

MOOTx01 runs in more than one place. A single estate might live only
on one iPhone. It might instead run against a shared PostgreSQL server
for a managed deployment. It might exist only for the length of a
test. Without a storage abstraction, every kit that touches memory
would need to know three different database APIs. Each API brings its
own quirks. Worse, a kit built against SQLite could not point at
PostgreSQL without a rewrite.

PersistenceKit solves this with one typed contract: the `Storage`
protocol. Three backends satisfy that contract: `PersistenceKitSQLite`,
`PersistenceKitPostgreSQL`, and `PersistenceKitInMemory`. A caller writes
against `Storage`, `RowStore`, `BlobStore`, and `AuditLog` exactly once.
Swapping the backend then means changing one line of configuration. The
calling code itself never changes.

One interface across three engines is still not enough on its own.
Real deployments also need at-rest encryption. They need tamper-evident
audit trails. They need safe deletion of sensitive content. They need a
cache that never lies about what storage truly holds. They need a way
to copy an entire estate from one backend to another. PersistenceKit
builds all of these as layers on top of the same four-operation core.
A kit that only needs plain rows never pays for features it does not
use. A kit that needs encryption or replication gets it without
leaving the same protocol surface.

PersistenceKit deliberately does not own vector similarity search. A
separate library, VectorKit, owns dense-embedding nearest-neighbor
search instead. PersistenceKit's job toward that workload stays narrow.
Every backend must support the storage needs a vector index has. It
must store a vector payload in a row. It must read many rows back in
bulk. It must count rows and delete rows. Call this the accommodation
contract.
PersistenceKit accommodates the workload. It does not implement the
search itself.

## How It Works

One protocol sits at the center of the library: `Storage`. A type
conforms to `Storage` by providing four sub-stores. `RowStore` handles
typed rows. `BlobStore` handles raw bytes keyed by string. `AuditLog`
keeps an append-only history of changes. `StorageObserver` delivers live
change notifications. A conforming type also manages schema and
transactions. `EstateConfiguration` tells a `Storage` implementation
which backend to open. It also sets the encryption mode and the cache
settings to use.

A schema in PersistenceKit is not raw SQL. It is a typed Swift value
called `SchemaDeclaration`, built from `TableDeclaration` and
`ColumnDeclaration` values. A kit that wants to store data describes
its tables once, as Swift structs. Each backend then translates that
description into its own native form. SQLite gets `CREATE TABLE`
statements. PostgreSQL gets `CREATE TABLE` statements in its own
dialect. The in-memory backend just allocates a dictionary. Queries
follow the same pattern. `StoragePredicate` is a closed tree of
comparison and bitmap operators. Each backend compiles that tree to its
own query language. No caller ever writes a raw SQL string.

Three backends satisfy `Storage`. `PersistenceKitSQLite` is the
on-device engine. It uses one file per estate, encrypted at rest
through a vendored SQLCipher build when an estate asks for
whole-database encryption. `PersistenceKitPostgreSQL` is the server
engine, built on the `postgres-nio` client. It gives each estate its
own PostgreSQL schema, so many estates can share one database server
without their tables colliding. `PersistenceKitInMemory` is the test
and prototyping engine. It keeps no file and opens no network
connection, and it disappears when the process exits.

The core protocol also carries decorators and free-function toolkits.
Any backend can use these without changing its own code.

- `CachingRowStore` wraps a `RowStore` with an in-memory hot tier. Rows
  above a configured sensitivity level are never cached. The cache
  guarantees that every read returns exactly what the backing store
  would return. Caching only changes speed, never correctness.
- `HashingRowStore` wraps a `RowStore` and computes a content hash on
  every write to a table marked hashable. It then reports the write up
  a parent chain, so a Merkle-style integrity tree can stay current
without a full rescan.

`DatasetStore` is a separate shaped surface on each backend.
Each dataset gets one table derived from its UUID.
User column names pass one strict identifier gate.
Queries reuse the normal predicate and ordering types.
- `RowCrypto` and its write and read seam functions apply per-row
  AES-GCM encryption to a table's `content` column. This runs whenever
  an estate is configured for row-level encryption, independent of
  whichever backend stores the row.
- `ErasureLedger` and `ErasureOverlay` implement right-to-be-forgotten
  deletion. The ledger records that a piece of content was erased. The
  overlay nulls that content out of every read afterward. It fails
  closed, dropping the row, if the erasure check itself cannot
  complete.
- `SnapshotRegistry` and `GCPin` let a kit take a named, attested
  snapshot of the estate's state. They also pin the oldest live
  snapshot's timestamp, so a later garbage-collection pass never
  deletes data a snapshot still needs.
- `PersistenceKitReplication` copies an entire estate's rows, audit
  events, and blobs from one `Storage` to another. It can run this
  copy as a one-shot full snapshot or as an incremental sync driven by
  a live change-observation session.

## How the Pieces Fit

Figure 1 shows the library's topology. It shows the major parts and
how a write or a read moves between them.

![Figure 1. Topology of PersistenceKit](topology.svg)

*Figure 1. Topology of PersistenceKit. A calling kit reaches storage
only through the four core protocols. Decorators wrap a backend's
`RowStore` transparently. Cross-cutting concerns sit beside the core
surface. Examples include encryption, erasure, and snapshots. Each is
invoked by name, not by inheritance. Replication reads one `Storage` and writes
another. Dashed regions mark backend-owned engines and the
cross-process encryption key store.*

A typical write enters through a kit-held reference to `any RowStore`.
If the estate has caching enabled, that reference is really a
`CachingRowStore` wrapping the real backend's row store. If hash-on-write
is also configured, a `HashingRowStore` may sit in front of that. The
write descends through zero or more decorators until it reaches the
concrete backend: `SQLiteRowStore`, `PostgreSQLRowStore`, or
`InMemoryRowStore`. That backend applies the row-encryption seam, a
no-op unless the estate uses row encryption. It checks the
content-and-keyID invariant. It validates every SQL identifier it is
about to interpolate. It then commits the row. Every backend notifies
its `StorageObserver` afterward. Decorators, cache invalidators, and
replication sessions downstream can then react.

A typical read follows the same path in reverse, with one addition.
Any content subject to the right-to-be-forgotten contract passes
through `ErasureOverlay.apply(...)` after the backend query returns. An
erased row's content columns then come back `nil`. The row's skeleton
still survives for referential integrity. The skeleton is its ID,
timestamps, and lattice anchors.

Encryption, erasure, and snapshots are not separate storage engines.
They are pure functions and small actors that any backend calls at the
right moment. `encryptedForWrite` and `decryptedForRead` run around a
write or a read. `ErasureLedgerOps.isErased` runs inside the overlay.
`GCPin.minimumRetainableHlc` runs before a vacuum. This design keeps
the cross-cutting logic in one place, the `PersistenceKit` core,
instead of duplicating it inside every backend. It is also why the
SQLite and PostgreSQL backends produce byte-compatible encrypted
envelopes. Both call the same `RowCrypto` code.

## What Ships in the Package

The package ships five Swift targets. `PersistenceKit` is the core. It
holds the `Storage`, `RowStore`, `BlobStore`, `AuditLog`, and
`StorageObserver` protocols. It holds the typed value and predicate
algebra, the schema declarations, and the caching and hashing
decorators. It also holds the encryption, erasure, snapshot, and
telemetry toolkits. `PersistenceKitInMemory` is the test backend.
`PersistenceKitSQLite` is the on-device backend, built against a
vendored `SQLCipher` C target. Whole-database encryption never depends
on the host operating system's own SQLite because of this.
`PersistenceKitPostgreSQL` is the server backend, built on `postgres-nio`
and `NIOSSL`. `PersistenceKitReplication` is the estate-copying
primitive. It depends only on the core protocol surface, so it works
against any pair of conforming backends.

The package also ships a Rust port in `rust/`. That port mirrors the
same trait surface. It mirrors the same closed predicate algebra. It
mirrors the same three backends. It exists for use outside the Swift
and Apple ecosystem. The two ports share design intent. They are not
conformance-gated against each
other the way LatticeLib's two legs are. PersistenceKit's cross-platform
contract is the shared protocol shape and wire format. It is not a
promise of byte-identical output from a shared corpus.
