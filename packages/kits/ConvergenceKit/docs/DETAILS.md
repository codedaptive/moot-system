---
doc: DETAILS
package: ConvergenceKit
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
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
    blob: 7dd43ccf5454739a3d1ae1cda0870cf507c479c6
  - path: Sources/ConvergenceKitFederation/FederationIdentity.swift
    blob: b64358fe36fd241049e9f5bdbbb335f1f3d29464
  - path: Sources/ConvergenceKitFederation/FederationSyncEngine.swift
    blob: 9bf722f8e7fc24ae12ac72d571c135da2b40af62
  - path: Sources/ConvergenceKitFederation/HyperplaneFamilyExchange.swift
    blob: 4880e847918c253f3d99a02fdd65c15f172d32e1
  - path: Sources/ConvergenceKitNone/ConvergenceKitNone.swift
    blob: 5d3556a08c8bc80ec6b824ceb4e0838db9c95ecc
---

# ConvergenceKit Details

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline
order: the shared types and protocol that every backend depends on, the
wire format, then the three backends from simplest to most complex.

## SyncTypes.swift

This file provides the core enumerations and value types shared by every
backend: `SyncDirection`, `ConflictPolicy`, `SyncedTable`, `SyncManifest`,
`SyncReceipt`, `SyncEvent`, `SyncState`, and `SyncError`.

`SyncDirection` states which way one table replicates: `bidirectional`,
`pushOnly`, or `pullOnly`. Declaring direction per table matters because not
every table should travel both ways; a table that only a phone should ever
write, for example, can be marked `pushOnly` from the phone and `pullOnly`
everywhere else, and a backend that receives such a table simply skips it.

`ConflictPolicy` states how a receiver resolves a row that changed on both
sides. `lastWriterWinsByHLC` is the default: it compares the Hybrid Logical
Clock, described in `OVERVIEW.md`, on the incoming record against the clock
on the stored row, and the more recent write survives. `appendOnly` treats
the table as a write-once audit log, where the row's own key already makes
duplicate delivery harmless, so incoming rows are always upserted rather
than compared. `localWins` and `remoteWins` are the two absolute policies,
useful when one side of a sync relationship is known in advance to be more
authoritative than the other.

`SyncedTable.init(name:direction:primaryKeyColumn:conflictPolicy:)` bundles
one table's replication rules; `direction` and `conflictPolicy` default to
`bidirectional` and `lastWriterWinsByHLC` so a simple table declaration only
needs a name and a primary key column. `SyncManifest` is the full
declaration for one sync session: a `kitID` and `schemaVersion` that
identify the application and its data version, a `zoneIdentifier` naming
where remote data lives (a CloudKit zone name or a federation-facing label),
and the list of `SyncedTable` entries. `SyncManifest.table(named:)` looks up
one table's rules by name; every backend calls this before deciding whether
and how to apply an incoming or outgoing change.

`SyncReceipt` summarizes one push or pull cycle: how many rows moved in each
direction, how many conflicts were detected, and when the cycle finished.
`SyncReceipt.empty` is the shared zero-value every backend returns when
there is nothing to do, so callers can treat "nothing happened" and "an
empty batch happened" identically.

`SyncEvent` is what a live subscription emits: remote changes applied, a
push completed, a peer connected or disconnected, or an error. `SyncState`
is the coarser summary a settings screen would bind to: disabled, enabled
with a zone and last-activity timestamps, actively syncing in a direction,
or stuck in an error with an optional retry time.

`SyncError` covers every failure a backend can report: not enabled yet,
already enabled, a schema or kit mismatch on an incoming record, a transport
failure, an encoding or decoding failure, an unreachable peer, a failed
authentication, an unsupported table, and `corruptRemoteIdentity`. That last
case exists for a specific reason explained where it is thrown, in
`CKRecordMapping.swift`: a remote record whose identifier cannot be read as
a valid row key must never be given a fabricated one, because a fabricated
identity would create a phantom row that never matches the real one on
later sync rounds. `SyncError` conforms to `Equatable` so tests can assert
exactly which error a call produced.

## SyncEngine.swift

This file provides `SyncEngine`, the protocol every backend conforms to. It
is the seam that lets application code, and the other two documents in this
package, describe "a backend" once instead of three times.

The protocol has four methods and one property. `enable(manifest:storage:)`
must be called once before any other method; it is where a backend
establishes remote subscriptions, if any, and starts observing local
writes. `disable()` tears the same things down and must be safe to call more
than once. `push()` and `pull()` are the one-shot operations that move data
in each direction and return a `SyncReceipt`. `subscribe()` returns an
`AsyncStream<SyncEvent>` for callers that want a live feed rather than
polling. `state` is a computed, asynchronously readable property for
user-interface bindings that only need the coarse `SyncState` snapshot.

Declaring `enable`/`push`/`pull` as `async throws` rather than synchronous
matters because every real backend performs network or disk work; declaring
the protocol this way once means every conforming backend, and every caller,
handles that uniformly.

## SyncRecord.swift

This file provides the wire format for one replicated row change:
`SyncRecord`, plus the smaller types it is built from.

`SyncRecord` carries a table name, an event kind, a row key, the changed
column values (or `nil` for a delete), an HLC, a schema version, and a kit
identifier. It declares its `CodingKeys` explicitly rather than relying on
Swift's automatic key derivation, because the SDK's Rust port decodes the
exact same JSON, using serde field renames that must match these strings
character for character. An automatic default would still work today, but
would silently break the two ports' agreement the moment either side's
naming convention changed without the other noticing.

`SyncEventKind` is a Codable, string-backed mirror of PersistenceKit's
`StorageEvent` (`insert`, `update`, `delete`). `SyncEventKind.init(from:)`
and `.asStorageEvent` convert both ways. A record's event kind is
duplicated here, rather than reusing `StorageEvent` directly, because
`StorageEvent` belongs to PersistenceKit and is not guaranteed to be a
stable wire format; `SyncEventKind` is ConvergenceKit's own promise about
what a byte on the wire means.

`PackedHLC` is a Codable wrapper around `SubstrateTypes.HLC`, exposing the
same three integer fields (`physicalTime`, `logicalCount`, `nodeID`)
directly, because the raw `HLC` type is not itself built for a stable wire
encoding.

`SyncValueMap` wraps `[String: TypedValue]`, PersistenceKit's row-value
type, as `[String: SyncValueBox]`. `TypedValue` covers thirteen kinds of
column value, several of which (an HLC, a fingerprint, a nested array) do
not map onto a single native JSON type, so `SyncValueBox` gives each value
an explicit `kind` string tag alongside its `payload`. This is an
adjacently-tagged encoding, matching Rust serde's
`#[serde(tag = "kind", content = "payload")]` attribute, chosen specifically
so a Rust decoder and a Swift decoder agree on the same JSON shape without
either language's default enum encoding leaking through.

`SyncValueBox.encode(to:)` and `.init(from:)` implement that tagging by
hand. Two cases deserve attention. The `.null` case omits the `payload` key
entirely on encode, matching how Rust serde omits content for a unit
variant; a decoder that expected a `payload` key here would fail against a
Rust-produced null. The `.timestamp` case converts a `Date` to whole epoch
seconds as an `Int64`, and guards the conversion first: `Int64(_:)` traps
the program on a `Double` that is not finite or is outside `Int64`'s range,
and a corrupt or maliciously crafted inbound record could carry such a
value. The guard turns a potential crash into a normal, catchable
`EncodingError` instead.

`FingerprintWire` is a Codable wrapper around `SubstrateTypes.Fingerprint256`
exposing its four 64-bit blocks directly, for the same reason `PackedHLC`
exists: the underlying substrate type is not itself declared Codable in a
form guaranteed stable across releases, so the wire format owns its own
copy of the shape.

## ConvergenceKitNone.swift

This file provides `NoSyncEngine`, the passthrough backend, and its private
`StateActor`.

`NoSyncEngine` conforms to `SyncEngine` with the smallest possible bodies:
`enable(manifest:storage:)` records that sync is on and remembers the
manifest, only for the sake of reporting a `SyncState`; `push()` and
`pull()` each check that `enable` was called and then return
`SyncReceipt.empty`; `subscribe()` returns a stream that never emits
anything, closed only when the caller cancels its own task. There is
nothing here to synchronize against, so the state lives in `StateActor`, a
Swift actor, purely so that `enable`/`disable`/`isEnabled` reads and writes
are safe to call from concurrent tasks without ConvergenceKit's own locking
code.

This backend exists so that development builds, unit tests, and genuinely
single-device deployments can enable the same `SyncManifest`-driven code
path as a production backend, without paying for a real remote connection
or writing separate no-sync branches throughout the application.

## CKRecordMapping.swift

This file provides `CKRecordMapping`, the generic translator between a
PersistenceKit row and Apple's `CKRecord`, and the two result types it
produces.

The mapping is generic rather than hand-written per table because a
CloudKit record type and its fields are derived entirely from data already
known at runtime: the table's name and the manifest's `kitID`.
`recordType(kitID:table:)` builds the CloudKit record type string as
`"\(kitID)_\(table)"`. `recordID(rowKey:zone:)` builds a `CKRecord.ID` from
the row's UUID and the zone. Because both functions are pure string and
identifier arithmetic, adding a new synced table to a manifest never
requires new mapping code in this file.

`record(from:table:rowKey:hlc:schemaVersion:kitID:zone:)` converts a row's
values into a `CKRecord`, then adds three reserved fields —
`_syncHLC`, `_syncSchemaVersion`, `_syncKitID` — that carry ConvergenceKit's
own metadata inside an otherwise ordinary CloudKit record. The private
`assign(value:to:forKey:)` helper switches over every `TypedValue` case; the
`.fingerprint` case packs the four 64-bit blocks into 32 bytes of
little-endian `Data`, and the `.array` case throws
`SyncError.encodingFailure`, because CKRecord has no native representation
for a nested list of typed values at this version.

`decode(_:)` reverses the process. It reads the three reserved fields back
out, and rejects a record missing `_syncHLC` or `_syncSchemaVersion` as a
decoding failure, because a record without sync metadata cannot be
attributed to any schema version or conflict-resolved correctly. It then
recovers the original table name from the `kitID_table` record type by
splitting on the first underscore. The `rowKey` recovery is the one place
this file enforces a hard rule explained in its own comment: if
`record.recordID.recordName` does not parse as a `UUID`, the function throws
`SyncError.corruptRemoteIdentity` rather than inventing a fresh UUID. A
fabricated identity would silently create a new local row every sync round,
because the fabricated UUID could never match the real one; refusing to
guess turns a slow, invisible data leak into a single visible error the
caller can log and skip.

`packed(_:)` and `unpacked(_:)` convert an `HLC` to and from a single
`Int64` for storage in a CloudKit `NSNumber` field, using a fixed bit
layout: 48 bits of physical time, 12 bits of logical count, 4 bits of node
identifier. The layout is a contract, not an implementation detail — any
change to the bit widths would make previously stored `_syncHLC` values
unreadable.

`SyncMeta` and `DecodedRecord` are the two small result types `decode(_:)`
returns: `SyncMeta` isolates the three reserved fields, and `DecodedRecord`
pairs them with the table name, row key, and clean application values (no
`_sync*` keys). Keeping the reserved fields out of `values` matters because
`values` round-trips directly into PersistenceKit's own row-value type,
which should never see ConvergenceKit's internal bookkeeping fields mixed
in with application data.

## CloudKitSyncEngine.swift

This file provides `CloudKitSyncEngine`, the CloudKit-backed `SyncEngine`
conformance, and its private `CloudKitStateActor`.

`CloudKitSyncEngine.init(containerIdentifier:)` accepts an optional CloudKit
container identifier, or `nil` to resolve `CKContainer.default()` later.
Resolution is deliberately deferred to `enable()` rather than done in
`init`, specifically so a test can construct the engine without an iCloud
entitlement configured; only calling `enable()`, `push()`, or `pull()`
actually touches CloudKit.

`CloudKitStateActor.enable(manifest:storage:)` creates the estate's CloudKit
zone (tolerating a "zone already exists" failure as expected, not an error)
and then, for every manifest table that is not `pullOnly`, subscribes to
`storage.observer.observe(table:events:)` and forwards every observed
`TableChange` into `pendingOutbound`. This is the mechanism, described in
`OVERVIEW.md`, that turns an ordinary PersistenceKit write into a queued
outbound sync record without the application calling anything sync-specific.

`push()` drains `pendingOutbound`, and for each change looks up the table's
`SyncedTable` in the manifest, skipping tables the manifest does not
declare or has marked `pullOnly`. For an insert or update it builds a
`CKRecord` via `CKRecordMapping.record`; for a delete it builds only the
`CKRecord.ID` to delete. The HLC used per record prefers one the change
already carries; if the observation carried none — true of the InMemory and
SQLite storage observers at this version — `push()` mints one from an
`HLCGenerator` seeded with a random node ID in `1...0x0F`, using
`send(now:)` rather than reading the clock without advancing it, so two
changes minted in the same push never collide on an identical timestamp.
The accumulated saves and deletes are sent together in one
`modifyRecords(saving:deleting:savePolicy:atomically:)` call.

`pull()` fetches everything new since the last stored `serverChangeToken`
via `recordZoneChanges(inZoneWith:since:)`, then applies each pulled record
through `applyInbound`. Three checks gate every record before it is
applied: the decoded `kitID` must match the manifest's, the decoded
`schemaVersion` must match, and the table must be declared in the manifest
and not marked `pushOnly`. Any of the three failing, or a decode error, is
caught, logged, and counted as a conflict rather than aborting the whole
pull — one bad record must not stop the rest of the batch from applying.
Deletions arrive as bare `CKRecord.ID` values with no record type attached,
so `pull()` cannot know which table a deleted record belonged to; it
attempts the delete against every manifest table that is not `pushOnly`,
using the manifest itself as the only scope guard.

`applyInbound(_:syncedTable:storage:)` is the method that actually enforces
`ConflictPolicy`. `.appendOnly` always upserts, relying on the row's own key
for idempotency. `.lastWriterWinsByHLC` reads back any existing row's stored
`_syncHLC` — which arrives as `.hlc` under the InMemory backend or as a raw
packed `.int` under SQLite and Postgres, because those schemas do not
declare the column's `TypedValue` kind — and silently discards the incoming
change if its HLC is older. A change that survives the comparison is
upserted with its own `_syncHLC`, `_syncSchemaVersion`, and `_syncKitID`
merged back into the row, which is what lets the *next* inbound write read
a comparison point back out. `.remoteWins` upserts unconditionally.
`.localWins` inserts only if no row with that primary key exists yet. The
method is declared with internal, not `private`, access specifically so LWW
(last-writer-wins) conflict tests can call it directly through
`@testable import`, bypassing the real CloudKit network stack.

## FederationIdentity.swift

This file provides the identity types federation pairing and signing are
built from: `PeerIdentity`, `LocalIdentity`, and `FederationSignature`.

`PeerIdentity` is a thin wrapper around a 32-byte Ed25519 public key,
carried for a paired peer. `LocalIdentity.init()` generates a fresh Ed25519
keypair in memory using `Curve25519.Signing.PrivateKey()`; every
`FederationSyncEngine` instance calls this once, at construction, so each
estate has its own persistent-for-the-session signing identity.
`LocalIdentity.init(privateKeyBytes:)` restores an identity from bytes a
caller already has, for a host application that wants to persist and reload
the estate's key across launches; this module deliberately does not persist
identity itself, leaving that decision to the host. `sign(_:)` produces an
Ed25519 signature over arbitrary data using the stored private key.

`FederationSignature.verify(_:of:by:)` checks a signature against a raw
32-byte public key. It returns `false`, rather than throwing, both when the
public key bytes do not form a valid Curve25519 key and when the signature
itself does not match — a receiver treats a malformed peer key exactly like
a bad signature, because both mean the message cannot be trusted.

## HyperplaneFamilyExchange.swift

This file provides three Codable value types for the pairing handshake:
`HyperplaneFamilySpec`, `PairingProposal`, and `PairingAcceptance`.

`HyperplaneFamilySpec` carries a `seed` and a `dimension` (default 256).
Two estates that share the same seed can each independently reproduce the
same hyperplane family — the substrate machinery, defined elsewhere, that
makes their 256-bit fingerprints directly comparable. Sharing only a seed,
rather than the derived family itself, keeps the pairing message small and
keeps the two sides' math guaranteed identical, because both derive it with
the same deterministic procedure from the same seed.

`PairingProposal` and `PairingAcceptance` model a proposer offering a family
spec plus a nonce (a value used once, to prevent a replay of an old
proposal), and an accepter countersigning that proposal. This file defines
only the shapes; as `FederationSyncEngine.swift` documents at its own
`pair(with:via:family:)` method, the shipped v1.0 pairing path does not yet
negotiate or sign these two structs — the caller supplies an
already-agreed `HyperplaneFamilySpec` directly. These types exist ahead of
that wiring so the wire shape is fixed and ready.

## FederationSyncEngine.swift

This file provides `FederationSyncEngine`, the Ed25519-authenticated
peer-to-peer `SyncEngine` backend; the envelope format its messages travel
in; the `Relay` transport abstraction and its in-process implementation;
and `FederationStateActor`, where the actual protocol logic lives.

`PayloadKind` is a one-byte discriminator identifying what an envelope
carries. `.syncRecordBatch` (`0x01`) is the only kind that exists at v1.0;
`.fieldWriteEventBatch` (`0x02`) is reserved in a code comment for a future
payload and must never be reassigned to anything else, because a receiver
on an older version would silently misinterpret a reused byte value.

`envelopeSigningBytes(senderPublicKey:payloadKind:payload:hlc:)` builds the
exact byte sequence a `SignedEnvelope`'s signature covers: the sender's
32-byte public key, the one-byte payload kind, a four-byte little-endian
payload length, the payload itself, and the HLC's three fields, each in a
fixed little-endian layout. Signing this constructed sequence, rather than
the raw JSON payload, closes what the code calls the relabel/replay seam: if
the signature only covered the payload bytes, an attacker who obtained a
valid signed payload could re-wrap it with a different sender key or
payload kind and have it re-verified as something it never was signed to
mean. The exact byte layout matters because the Rust port's
`envelope_signing_bytes` function must reproduce these bytes exactly, or a
signature made on one port fails verification on the other.

`SignedEnvelope` is the wire message this signature protects: the sender's
public key, the payload kind, the opaque payload, the signature, and a
batch-level HLC. The batch HLC is minted after every record's own HLC, in
`push()`, so it is strictly later — a reader can trust the envelope's HLC as
a batch watermark distinct from any single record's HLC inside it.

`Relay` is a two-method protocol — `send(to:message:)` and
`drain(for:)` — that abstracts the transport a `SignedEnvelope` travels
over. `FederationRelay` is the only conformer shipped at v1.0: an in-process,
lock-protected dictionary from a recipient's public key to a list of pending
envelopes. It exists so the full pairing, signing, and conflict-resolution
protocol can be exercised in tests without a real network, and so a future
hosted relay (a `SyncServer`) can conform to the same protocol as a drop-in
replacement without touching `FederationSyncEngine` itself.

`FederationStateActor` is where enable, pairing, push, and pull are
implemented, as a Swift actor so its mutable state — peers, pending
outbound changes, subscribers — is safe under concurrent access without
manual locking. `enable(manifest:storage:)` mirrors
`CloudKitStateActor.enable`: it subscribes to `storage.observer.observe` for
every push-eligible table and appends observed changes to
`pendingOutbound`. `disable()` differs from the CloudKit actor in one
deliberate way: it cancels every observer task and then *awaits* each one's
completion before clearing state, rather than only cancelling. The code
comment explains why — without awaiting, a change already in flight inside
a cancelled task could still land in the outbox after `disable()` returned,
which would violate the guarantee that disabling sync stops capturing
writes immediately.

`pair(with:via:family:)` registers the peer's public key, relay, and family
spec locally, then calls `acceptPeering` on the peer's own actor so the
relationship is recorded symmetrically on both sides from one call — a test
or a caller need not call `pair` twice. Pairing here is a local bookkeeping
step, not yet the signed `PairingProposal`/`PairingAcceptance` handshake
`HyperplaneFamilyExchange.swift` defines the shapes for.

`push()` converts every pending `TableChange` into a `SyncRecord` (minting
an HLC the same way `CloudKitStateActor.push()` does, and for the same
reason), JSON-encodes the batch, builds and signs an envelope, and hands it
to every paired peer's relay. `pull()` drains each peer's relay inbox and,
for every envelope, runs four checks in order before trusting its contents:
the sender's public key must match the specific peer it arrived from (not
merely any known peer — the code comment cites ADR-013, since a valid
self-signature alone does not prove the pairing handshake was completed);
the payload kind must be a known one; the signature must verify over the
reconstructed canonical bytes; and only then is the JSON payload decoded.
A record that passes all four still individually re-checks its `kitID` and
`schemaVersion` against the manifest and its table against the manifest's
declared tables, exactly like `CloudKitSyncEngine.pull()`, before reaching
`applyInbound`.

`applyInbound(_:syncedTable:storage:)` implements the same four
`ConflictPolicy` arms as `CloudKitStateActor.applyInbound`, extended to
cover delete events explicitly (`CloudKitSyncEngine` receives deletes as
bare CloudKit record IDs with no policy dispatch; federation's `SyncRecord`
always carries an explicit event kind, so its delete path can apply a
policy the same way inserts and updates do). Under
`.lastWriterWinsByHLC`, a stale delete — one whose HLC is older than the
row's stored `_syncHLC` — is silently rejected rather than removing a row a
peer has since updated; `.appendOnly` rejects every remote delete
outright, because an append-only table is write-once by definition;
`.remoteWins` deletes unconditionally; `.localWins` rejects every remote
delete, leaving local state authoritative. The method is internal, not
`private`, for the same testing reason as its CloudKit counterpart.

## Rust Port and Conformance

The `rust/` directory contains a second implementation of most of this
package: the core types, the `SyncRecord` wire format, the `SyncEngine`
trait, and both the None and Federation backends, in `rust/src/types.rs`,
`record.rs`, `engine.rs`, `none.rs`, `federation.rs`, and `pairing.rs`.
CloudKit is Apple-only and has no Rust equivalent by design; the Swift
package alone handles that transport, as `rust/README.md` and
`rust/src/lib.rs` both state explicitly.

The Rust and Swift Federation backends are gated by shared behavior rather
than a byte-fixture file the way some other SDK packages are: both ports
implement observer-driven outbox population, the same four `ConflictPolicy`
arms in `apply_record`/`applyInbound`, and identical Ed25519 envelope
signing bytes in `envelope_signing_bytes`/`envelopeSigningBytes`. The Rust
test suite (`rust/tests/`) exercises wire-format round trips, last-writer-
wins ordering, and inbound event routing per conflict policy, each
described in `rust/README.md` as mirroring a named Swift test file. When you
change conflict resolution, the envelope signing byte layout, or a
`SyncRecord` field name in either port, update both and re-run both test
suites — the JSON field names and the signing byte layout are the contract
between them.
