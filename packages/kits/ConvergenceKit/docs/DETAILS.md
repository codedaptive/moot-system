---
doc: DETAILS
package: ConvergenceKit
repo: moot-system
authored_commit: f1c1f3bf8dafd26faf5df26c2ddf2ea909e2df18
authored_date: 2026-07-23
sources:
  - path: Sources/ConvergenceKit/SyncEngine.swift
    blob: 32e69e0ea5002d8b699866183371527f675023cd
  - path: Sources/ConvergenceKit/SyncRecord.swift
    blob: 8c29f63ea9f965f1ea75d3bbff71632f76699233
  - path: Sources/ConvergenceKit/SyncTypes.swift
    blob: 52ed3cb07f0483d0e541b86a8da080791402d132
  - path: Sources/ConvergenceKitCloudKit/CKRecordMapping.swift
    blob: 9b61903296533f8044bee5a4f93ca2dc1b76b653
  - path: Sources/ConvergenceKitCloudKit/CloudKitSyncEngine.swift
    blob: a6f9215f2502a6b6bf382fb0d15f87f1f0c22da7
  - path: Sources/ConvergenceKitFederation/FederationIdentity.swift
    blob: b64358fe36fd241049e9f5bdbbb335f1f3d29464
  - path: Sources/ConvergenceKitFederation/FederationSyncEngine.swift
    blob: 413408bb31abb9079f767e2926f0c86eb7f829d6
  - path: Sources/ConvergenceKitFederation/HyperplaneFamilyExchange.swift
    blob: 4880e847918c253f3d99a02fdd65c15f172d32e1
  - path: Sources/ConvergenceKitNone/ConvergenceKitNone.swift
    blob: 5d3556a08c8bc80ec6b824ceb4e0838db9c95ecc
---

# ConvergenceKit Details

## Current Release Details

The CloudKit engine no longer writes sync meta columns into application rows.
It reads and writes `_ck_sync_meta` by table name and primary key.
A remote record with an older HLC is skipped.
A newer record updates the row.
The engine then stores its sync HLC in the side table.

CloudKit creates `_ck_sync_meta` through `Storage.migrate`.
It does this before observers start or a pull can run.
The application schema remains active.

Federation treats the paired peer registry as the trust source.
It checks the claimed sender key against the registered key.
Canonical bytes and signature checks use the registered key.

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline
order. First come the shared types and the protocol every backend depends
on. Next comes the wire format. Then come the three backends, from
simplest to most complex.

## SyncTypes.swift

This file provides the core enumerations and value types shared by every
backend: `SyncDirection`, `ConflictPolicy`, `SyncedTable`, `SyncManifest`,
`SyncReceipt`, `SyncEvent`, `SyncState`, and `SyncError`.

`SyncDirection` states which way one table replicates. The three choices
are `bidirectional`, `pushOnly`, and `pullOnly`. Direction matters per
table because not every table should travel both ways. A phone-only table
can be marked `pushOnly` from the phone. Every other device marks that same
table `pullOnly`. A backend that receives such a table simply skips it.

`ConflictPolicy` states how a receiver resolves a row that changed on both
sides. `lastWriterWinsByHLC` is the default. It compares the Hybrid
Logical Clock, described in `OVERVIEW.md`, on the incoming record against
the clock on the stored row. The more recent write survives. `appendOnly`
treats the table as a write-once audit log. The row's own key already
makes duplicate delivery harmless, so incoming rows are always upserted
rather than compared. `localWins` and `remoteWins` are the two absolute
policies. They suit a case where one side of a sync relationship is known
in advance to be more authoritative than the other.

`SyncedTable.init(name:direction:primaryKeyColumn:conflictPolicy:)`
bundles one table's replication rules. `direction` and `conflictPolicy`
default to `bidirectional` and `lastWriterWinsByHLC`. A simple table
declaration therefore needs only a name and a primary key column.
`SyncManifest` is the full declaration for one sync session. It carries a
`kitID` and a `schemaVersion` that identify the application and its data
version. It carries a `zoneIdentifier` naming where remote data lives,
either a CloudKit zone name or a federation-facing label. It carries the
list of `SyncedTable` entries. `SyncManifest.table(named:)` looks up one
table's rules by name. Every backend calls this before it decides whether
and how to apply an incoming or outgoing change.

`SyncReceipt` summarizes one push or pull cycle. It reports how many rows
moved in each direction, how many conflicts turned up, and when the cycle
finished. `SyncReceipt.empty` is the shared zero-value every backend
returns when there is nothing to do. Callers can then treat "nothing
happened" and "an empty batch happened" the same way.

`SyncEvent` is what a live subscription emits: remote changes applied, a
push completed, a peer connected, a peer disconnected, or an error.
`SyncState` is the coarser summary a settings screen would bind to. One
case is disabled. One case is enabled, carrying a zone and last-activity
timestamps. One case is actively syncing in a direction. One case is
stuck in an error, carrying an optional retry time.

`SyncError` covers every failure a backend can report: not enabled yet,
already enabled, a transport failure, an encoding failure, a decoding
failure, an unreachable peer, a failed authentication, an unsupported
table, and `corruptRemoteIdentity`. It also covers a schema mismatch and
a kit mismatch on an incoming record. `corruptRemoteIdentity` exists for a
specific reason. `CKRecordMapping.swift` explains that reason where the
error is thrown. A remote record
whose identifier cannot be read as a valid row key must never be given a
fabricated one. A fabricated identity would create a phantom row. That
phantom row would never match the real one on later sync rounds.
`SyncError` conforms to `Equatable` so tests can assert exactly which error
a call produced.

## SyncEngine.swift

This file provides `SyncEngine`, the protocol every backend conforms to.
It is the seam that lets application code, and the other two documents in
this package, describe "a backend" once instead of three times.

The protocol has four methods and one property.
`enable(manifest:storage:)` must be called once before any other method.
It is where a backend establishes remote subscriptions, if any, and starts
observing local writes. `disable()` tears the same things down. It must be
safe to call more than once. `push()` and `pull()` are the one-shot
operations that move data in each direction. Each returns a `SyncReceipt`.
`subscribe()` returns an `AsyncStream<SyncEvent>` for callers that want a
live feed rather than polling. `state` is a computed, asynchronously
readable property. It suits a user interface that only needs the coarse
`SyncState` snapshot.

`enable`, `push`, and `pull` are declared `async throws` rather than
synchronous. Every real backend performs network or disk work, so this
choice matters. Declaring the protocol this way once means every
conforming backend, and every caller, handles that work the same way.

## SyncRecord.swift

This file provides the wire format for one replicated row change:
`SyncRecord`, plus the smaller types it is built from.

`SyncRecord` carries a table name, an event kind, a row key, an HLC, a
schema version, and a kit identifier. It also carries the changed column
values, or `nil` for a delete. It declares its `CodingKeys` explicitly. It does not rely on
Swift's automatic key derivation. The SDK's Rust port decodes the exact
same JSON, using serde field renames that must match these strings
character for character. An automatic default would still work today. It
would silently break the two ports' agreement the moment either side's
naming convention changed without the other noticing.

`SyncEventKind` is a Codable, string-backed mirror of PersistenceKit's
`StorageEvent`. It has three cases: `insert`, `update`, and `delete`.
`SyncEventKind.init(from:)`
and `.asStorageEvent` convert both ways. A record's event kind is
duplicated here rather than reusing `StorageEvent` directly. `StorageEvent`
belongs to PersistenceKit. It is not guaranteed to be a stable wire
format. `SyncEventKind` is ConvergenceKit's own promise about what a byte
on the wire means.

`PackedHLC` is a Codable wrapper around `SubstrateTypes.HLC`. It exposes
the same three integer fields directly: `physicalTime`, `logicalCount`,
and `nodeID`. It exists because the raw `HLC` type is not itself built for
a stable wire encoding.

`SyncValueMap` wraps `[String: TypedValue]`, PersistenceKit's row-value
type, as `[String: SyncValueBox]`. `TypedValue` covers thirteen kinds of
column value. Several of these do not map onto a single native JSON type.
They include an HLC, a fingerprint, and a nested array. `SyncValueBox`
gives each value an explicit `kind` string tag alongside its `payload`.
This is an adjacently-tagged encoding. It matches Rust serde's
`#[serde(tag = "kind", content = "payload")]` attribute. The SDK chose it
specifically so a Rust decoder and a Swift decoder agree on the same JSON
shape, without either language's default enum encoding leaking through.

`SyncValueBox.encode(to:)` and `.init(from:)` implement that tagging by
hand. Two cases deserve attention. The `.null` case omits the `payload`
key entirely on encode. This matches how Rust serde omits content for a
unit variant. A decoder that expected a `payload` key here would fail
against a Rust-produced null. The `.timestamp` case converts a `Date` to
whole epoch seconds as an `Int64`. It guards the conversion first.
`Int64(_:)` traps the program on a `Double` that is not finite or is
outside `Int64`'s range. A corrupt or maliciously crafted inbound record
could carry such a value. The guard turns a potential crash into a
normal, catchable `EncodingError` instead.

`FingerprintWire` is a Codable wrapper around
`SubstrateTypes.Fingerprint256`. It exposes that type's four 64-bit blocks
directly, for the same reason `PackedHLC` exists. The underlying substrate
type is not itself declared Codable in a form guaranteed stable across
releases, so the wire format owns its own copy of the shape.

## ConvergenceKitNone.swift

This file provides `NoSyncEngine`, the passthrough backend, and its
private `StateActor`.

`NoSyncEngine` conforms to `SyncEngine` with the smallest possible bodies.
`enable(manifest:storage:)` records that sync is on and remembers the
manifest, only for the sake of reporting a `SyncState`. `push()` and
`pull()` each check that `enable` was called, then return
`SyncReceipt.empty`. `subscribe()` returns a stream that never emits
anything. It closes only when the caller cancels its own task. There is
nothing here to synchronize against, so the state lives in `StateActor`, a
Swift actor. It exists so that `enable`, `disable`, and `isEnabled` reads
and writes stay safe under concurrent calls. ConvergenceKit needs no
locking code of its own to guarantee that safety.

This backend exists so that development builds, unit tests, and genuinely
single-device deployments can enable the same `SyncManifest`-driven code
path as a production backend. They do this without paying for a real
remote connection, and without writing separate no-sync branches
throughout the application.

## CKRecordMapping.swift

This file provides `CKRecordMapping`, the generic translator between a
PersistenceKit row and Apple's `CKRecord`, and the two result types it
produces.

The mapping is generic rather than hand-written per table. A CloudKit
record type and its fields derive entirely from data already known at
runtime: the table's name and the manifest's `kitID`.
`recordType(kitID:table:)` builds the CloudKit record type string as
`"\(kitID)_\(table)"`. `recordID(rowKey:zone:)` builds a `CKRecord.ID` from
the row's UUID and the zone. Both functions are pure string and identifier
arithmetic. Adding a new synced table to a manifest never requires new
mapping code in this file.

`record(from:table:rowKey:hlc:schemaVersion:kitID:zone:)` converts a row's
values into a `CKRecord`. It then adds three reserved fields: `_syncHLC`,
`_syncSchemaVersion`, and `_syncKitID`. These fields carry ConvergenceKit's
own metadata inside an otherwise ordinary CloudKit record. The private
`assign(value:to:forKey:)` helper switches over every `TypedValue` case.
The `.fingerprint` case packs the four 64-bit blocks into thirty-two bytes
of little-endian `Data`. The `.array` case throws
`SyncError.encodingFailure`, because `CKRecord` has no native
representation for a nested list of typed values at this version.

`decode(_:)` reverses the process. It reads the three reserved fields back
out. It rejects a record missing `_syncHLC` or `_syncSchemaVersion` as a
decoding failure. A record without sync metadata cannot be attributed to
any schema version. It also cannot be conflict-resolved correctly. The
function then recovers the original table name from the `kitID_table`
record type, by splitting on the first underscore. The `rowKey` recovery
is the one place this file enforces a hard rule, explained in its own
comment. If `record.recordID.recordName` does not parse as a `UUID`, the
function throws `SyncError.corruptRemoteIdentity` rather than inventing a
fresh UUID. A fabricated identity would silently create a new local row
every sync round, because the fabricated UUID could never match the real
one. Refusing to guess turns a slow, invisible data leak into a single
visible error the caller can log and skip.

`packed(_:)` and `unpacked(_:)` convert an `HLC` to and from a single
`Int64` for storage in a CloudKit `NSNumber` field. They use a fixed bit
layout: forty-eight bits of physical time, twelve bits of logical count,
and four bits of node identifier. This layout is a contract, not an
implementation detail. Any change to the bit widths would make previously
stored `_syncHLC` values unreadable.

`SyncMeta` and `DecodedRecord` are the two small result types `decode(_:)`
returns. `SyncMeta` isolates the three reserved fields. `DecodedRecord`
pairs them with the table name, row key, and clean application values,
with no `_sync*` keys. Keeping the reserved fields out of `values` matters,
because `values` round-trips directly into PersistenceKit's own row-value
type. That type should never see ConvergenceKit's internal bookkeeping
fields mixed in with application data.

## CloudKitSyncEngine.swift

This file provides `CloudKitSyncEngine`, the CloudKit-backed `SyncEngine`
conformance, and its private `CloudKitStateActor`.

`CloudKitSyncEngine.init(containerIdentifier:)` accepts an optional
CloudKit container identifier, or `nil` to resolve `CKContainer.default()`
later. Resolution is deliberately deferred to `enable()` rather than done
in `init`. This lets a test construct the engine without an iCloud
entitlement configured. Only calling `enable()`, `push()`, or `pull()`
actually touches CloudKit.

`CloudKitStateActor.enable(manifest:storage:)` creates the estate's
CloudKit zone. It tolerates a "zone already exists" failure as expected,
not as an error. Then, for every manifest table that is not `pullOnly`, it
subscribes to `storage.observer.observe(table:events:)` and forwards every
observed `TableChange` into `pendingOutbound`. `OVERVIEW.md` describes this
mechanism. It turns an ordinary PersistenceKit write into a queued outbound
sync record. The application need not call anything sync-specific for
that to happen.

`push()` drains `pendingOutbound`. For each change, it looks up the
table's `SyncedTable` in the manifest. It skips tables the manifest does
not declare, or has marked `pullOnly`. For an insert or update it builds a
`CKRecord` via `CKRecordMapping.record`. For a delete it builds only the
`CKRecord.ID` to delete. The HLC used per record prefers one the change
already carries. If the observation carried none, true of the InMemory
and SQLite storage observers at this version, `push()` mints one from an
`HLCGenerator` seeded with a random node ID in `1...0x0F`. It uses
`send(now:)` rather than reading the clock without advancing it, so two
changes minted in the same push never collide on an identical timestamp.
The accumulated saves and deletes go out together in one
`modifyRecords(saving:deleting:savePolicy:atomically:)` call.

`pull()` fetches everything new since the last stored `serverChangeToken`,
via `recordZoneChanges(inZoneWith:since:)`. It then applies each pulled
record through `applyInbound`. Three checks gate every record before it is
applied. The decoded `kitID` must match the manifest's own `kitID`. The
decoded `schemaVersion` must match. The table must be declared in the
manifest and not marked `pushOnly`. A failure on any of the three checks,
or a decode error, gets caught and logged. The record then counts as a
conflict, rather than aborting the whole pull. One bad record must not stop the rest of the
batch from applying. Deletions arrive as bare `CKRecord.ID` values with no
record type attached. `pull()` therefore cannot know which table a
deleted record belonged to. It attempts the delete against every manifest
table that is not `pushOnly`, using the manifest itself as the only scope
guard.

`applyInbound(_:syncedTable:storage:)` is the method that actually
enforces `ConflictPolicy`. `.appendOnly` always upserts. It relies on the
row's own key for idempotency. `.lastWriterWinsByHLC` reads back any
existing row's stored `_syncHLC`. Under the InMemory backend this arrives
as `.hlc`. Under SQLite and Postgres it arrives as a raw packed `.int`,
because those schemas do not declare the column's `TypedValue` kind. The
method discards the incoming change silently if its HLC is older. A
change that survives the comparison gets upserted. Its own `_syncHLC`,
`_syncSchemaVersion`, and `_syncKitID` merge back into the row. That
merge is what lets the next inbound write read a comparison point back
out. `.remoteWins` upserts unconditionally. `.localWins` inserts only if
no row with that primary key exists yet. The method is declared with
internal access, not `private` access. It is declared this way specifically so
last-writer-wins conflict tests can call it directly through `@testable
import`, bypassing the real CloudKit network stack.

## FederationIdentity.swift

This file provides the identity types federation pairing and signing are
built from: `PeerIdentity`, `LocalIdentity`, and `FederationSignature`.

`PeerIdentity` is a thin wrapper around a thirty-two-byte Ed25519 public
key, carried for a paired peer. `LocalIdentity.init()` generates a fresh
Ed25519 keypair in memory, using `Curve25519.Signing.PrivateKey()`. Every
`FederationSyncEngine` instance calls this once, at construction, so each
estate has its own signing identity for the session.
`LocalIdentity.init(privateKeyBytes:)` restores an identity from bytes a
caller already has. This suits a host application that wants to persist
and reload the estate's key across launches. This module deliberately
does not persist identity itself. It leaves that decision to the host.
`sign(_:)` produces an Ed25519 signature over arbitrary data, using the
stored private key.

`FederationSignature.verify(_:of:by:)` checks a signature against a raw
thirty-two-byte public key. It returns `false`, rather than throwing, in
two cases. It returns `false` when the public key bytes do not form a
valid Curve25519 key. It returns `false` when the signature itself does
not match. A receiver treats a malformed peer key exactly like a bad
signature, because both mean the message cannot be trusted.

## HyperplaneFamilyExchange.swift

This file provides three Codable value types for the pairing handshake:
`HyperplaneFamilySpec`, `PairingProposal`, and `PairingAcceptance`.

`HyperplaneFamilySpec` carries a `seed` and a `dimension`, which defaults
to two hundred fifty-six. Two estates that share the same seed can each
independently reproduce the same hyperplane family. That family is the
substrate machinery, defined elsewhere, that makes their 256-bit
fingerprints directly comparable. Sharing only a seed, rather than the
derived family itself, keeps the pairing message small. It also keeps the
two sides' math guaranteed identical, because both sides derive it with
the same deterministic procedure from the same seed.

`PairingProposal` and `PairingAcceptance` model a handshake between two
roles. A proposer offers a family spec plus a nonce, a value used once to
prevent a replay of an old proposal. An accepter countersigns that
proposal. This file defines only the shapes. As `FederationSyncEngine.swift`
documents at its own `pair(with:via:family:)` method, the shipped v1.0
pairing path does not yet negotiate or sign these two structs. The caller
supplies an already-agreed `HyperplaneFamilySpec` directly instead. These
types exist ahead of that wiring, so the wire shape is fixed and ready.

## FederationSyncEngine.swift

The receive path resolves trust from the pairing registry.
An envelope cannot choose the key that verifies its own signature.
The claimed sender key must match the paired key.
Only then does the engine verify canonical bytes and apply records.

This file provides `FederationSyncEngine`, the Ed25519-authenticated
peer-to-peer `SyncEngine` backend. It also provides the envelope format
its messages travel in. It provides the `Relay` transport abstraction and
its in-process implementation. It provides `FederationStateActor`, where
the actual protocol logic lives.

`PayloadKind` is a one-byte discriminator identifying what an envelope
carries. `.syncRecordBatch` (`0x01`) is the only kind that exists at
v1.0. `.fieldWriteEventBatch` (`0x02`) is reserved in a code comment for a
future payload. It must never be reassigned to anything else, because a
receiver on an older version would silently misinterpret a reused byte
value.

`envelopeSigningBytes(senderPublicKey:payloadKind:payload:hlc:)` builds
the exact byte sequence a `SignedEnvelope`'s signature covers. The
sequence starts with the sender's public key, thirty-two bytes long.
Next comes the one-byte payload kind, then a four-byte little-endian
payload length, then the payload itself. Last comes the HLC's three
fields, each in a fixed little-endian layout. Signing this constructed
sequence, rather than the raw JSON payload, closes what the code calls
the relabel and replay seam. If the signature only covered the
payload bytes, an attacker who obtained a valid signed payload could
re-wrap it with a different sender key or payload kind. That re-wrapped
message would then re-verify as something it never was signed to mean.
The exact byte layout matters, because the Rust port's
`envelope_signing_bytes` function must reproduce these bytes exactly. A
signature made on one port would otherwise fail verification on the
other.

`SignedEnvelope` is the wire message this signature protects: the
sender's public key, the payload kind, the opaque payload, the signature,
and a batch-level HLC. The batch HLC is minted after every record's own
HLC, in `push()`, so it is strictly later. A reader can trust the
envelope's HLC as a batch watermark distinct from any single record's HLC
inside it.

`Relay` is a two-method protocol, `send(to:message:)` and `drain(for:)`,
that abstracts the transport a `SignedEnvelope` travels over.
`FederationRelay` is the only conformer shipped at v1.0. It is an
in-process, lock-protected dictionary from a recipient's public key to a
list of pending envelopes. It exists so the full pairing, signing, and
conflict-resolution protocol can be exercised in tests without a real
network. It also lets a future hosted relay, a `SyncServer`, conform to
the same protocol as a drop-in replacement. That replacement need not
touch `FederationSyncEngine` itself.

This file provides `FederationStateActor`. It implements four operations:
enable, pairing, push, and pull. It is a Swift actor. This keeps its
mutable state safe under concurrent access, without manual locking code.
That state includes peers, pending outbound changes, and subscribers.
`enable(manifest:storage:)` mirrors
`CloudKitStateActor.enable`. It subscribes to `storage.observer.observe`
for every push-eligible table and appends observed changes to
`pendingOutbound`. `disable()` differs from the CloudKit actor in one
deliberate way. It cancels every observer task, and then awaits each
one's completion, before it clears state. It does not merely cancel. The
code comment explains why. Without awaiting, a change already in flight
inside a cancelled task could still land in the outbox after `disable()`
returned. That would violate the guarantee that disabling sync stops
capturing writes immediately.

`pair(with:via:family:)` registers the peer's public key, relay, and
family spec locally. It then calls `acceptPeering` on the peer's own
actor, so the relationship gets recorded symmetrically on both sides from
one call. A test or a caller need not call `pair` twice. Pairing here is a
local bookkeeping step. It is not yet the signed
`PairingProposal`/`PairingAcceptance` handshake `HyperplaneFamilyExchange.swift`
defines the shapes for.

`push()` converts every pending `TableChange` into a `SyncRecord`, minting
an HLC the same way `CloudKitStateActor.push()` does, and for the same
reason. It JSON-encodes the batch, builds and signs an envelope, and
hands it to every paired peer's relay. `pull()` drains each peer's relay
inbox. For every envelope, it runs four checks in order before trusting
its contents. First, the sender's public key must match the specific peer
it arrived from, not merely any known peer. The code comment cites
ADR-013 here, since a valid self-signature alone does not prove the
pairing handshake was completed. Second, the payload kind must be a known
one. Third, the signature must verify over the reconstructed canonical
bytes. Only then, fourth, is the JSON payload decoded. A record that
passes all four checks still faces individual checks after that. It must
match the manifest's `kitID` and `schemaVersion`. Its table must appear
among the manifest's declared tables, exactly as in
`CloudKitSyncEngine.pull()`. Only a record that clears every check reaches
`applyInbound`.

`applyInbound(_:syncedTable:storage:)` implements the same four
`ConflictPolicy` arms as `CloudKitStateActor.applyInbound`. It extends
them to cover delete events explicitly. `CloudKitSyncEngine` receives
deletes as bare CloudKit record IDs, with no policy dispatch. Federation's
`SyncRecord` always carries an explicit event kind, so its delete path can
apply a policy the same way inserts and updates do. Under
`.lastWriterWinsByHLC`, a stale delete is silently rejected. A stale
delete is one whose HLC is older than the row's stored `_syncHLC`. This
avoids removing a row a peer has since updated. `.appendOnly` rejects every
remote delete outright, because an append-only table is write-once by
definition. `.remoteWins` deletes unconditionally. `.localWins` rejects
every remote delete, leaving local state authoritative. The method is
internal, not `private`, for the same testing reason as its CloudKit
counterpart.

## Rust Port and Conformance

The `rust/` directory contains a second implementation of most of this
package. It covers the core types, the wire format, and the protocol
trait. It also covers the None and Federation backends, ported to Rust.
These live in `rust/src/types.rs`, `record.rs`, `engine.rs`, `none.rs`,
`federation.rs`, and `pairing.rs`. CloudKit is Apple-only and has no Rust
equivalent by design. The Swift package alone handles that transport, as
`rust/README.md` and `rust/src/lib.rs` both state explicitly.

The Rust and Swift Federation backends are gated by shared behavior. They
are not gated by a byte-fixture file, the way some other SDK packages
are. Both ports implement observer-driven outbox population. Both
implement the same four `ConflictPolicy` arms, in `apply_record` and
`applyInbound`. Both implement identical Ed25519 envelope signing bytes,
in `envelope_signing_bytes` and `envelopeSigningBytes`. The Rust test
suite lives in `rust/tests/`. It exercises wire-format round trips,
last-writer-wins ordering, and inbound event routing per conflict policy.
Each test is described in `rust/README.md` as mirroring a named Swift test
file. Update both ports together whenever you change conflict resolution,
the envelope signing byte layout, or a `SyncRecord` field name in either
one. Then re-run both test suites. The JSON field names and the signing
byte layout are the contract between them.
