---
doc: AGENT_MAP
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

# AGENT_MAP: ConvergenceKit

PURPOSE: replicates PersistenceKit rows across device/estate boundaries. One protocol (SyncEngine), three interchangeable backends (None/CloudKit/Federation). App writes only to PersistenceKit; backend observes via StorageObserver, ships via SyncRecord wire format, applies inbound through PersistenceKit's own write path (RowStore.upsert/insert/delete), which fires StorageObserver again on the receiving side.

DEPS: core target imports SubstrateTypes (HLC, Fingerprint256), PersistenceKit (Storage, StorageObserver, TableChange, TypedValue). ConvergenceKitCloudKit adds CloudKit, os. ConvergenceKitFederation adds Crypto (swift-crypto, Ed25519), os. ConvergenceKitNone adds nothing beyond core. Imported by: NONE within the SDK at this commit: application-layer composition only; QueueKit's Package.swift explicitly excludes it as a dependency (spec §11, DECISION_KIT_GRAPH_REFACTOR_2026-05-19.md). Rust port in rust/ mirrors core types + wire format + None/Federation backends; CloudKit has no Rust port (Apple-only, by design).

ENTRY POINTS (most callers need only these):
- SyncEngine.swift:26 `SyncEngine.enable(manifest:storage:) async throws`: call once before push/pull/subscribe
- SyncEngine.swift:34/:38 `push() async throws -> SyncReceipt` / `pull() async throws -> SyncReceipt`: one-shot cycles
- SyncEngine.swift:41 `subscribe() -> AsyncStream<SyncEvent>`: live feed for UI
- FederationSyncEngine.swift:80 `FederationSyncEngine.pair(with:via:family:)`: Federation-only, establishes peer relationship before push/pull do anything

## Symbol Table

### Protocol: SyncEngine.swift
- :21 `protocol SyncEngine: Sendable`: 4 methods + 1 async property, every backend conforms
- :26 `enable(manifest:storage:) async throws`: starts local-write observation; establishes remote subscriptions if any
- :30 `disable() async throws`: idempotent teardown
- :34 `push()` / :38 `pull()`: one-shot, return SyncReceipt
- :41 `subscribe() -> AsyncStream<SyncEvent>`
- :44 `state: SyncState { get async }`

### Core types: SyncTypes.swift
- :22 `enum SyncDirection: String`: bidirectional | pushOnly | pullOnly
- :29 `enum ConflictPolicy: String`: lastWriterWinsByHLC (default) | appendOnly | localWins | remoteWins
- :42 `struct SyncedTable`: name/direction/primaryKeyColumn/conflictPolicy; :54 init defaults direction=.bidirectional, conflictPolicy=.lastWriterWinsByHLC
- :70 `struct SyncManifest`: kitID/schemaVersion/zoneIdentifier/tables; :94 `table(named:) -> SyncedTable?`
- :100 `struct SyncReceipt`: pushed/pulled/conflicts/timestamp; :113 `.empty` shared zero-value
- :117 `enum SyncEvent`: .remoteChangesApplied(count:) / .pushCompleted(receipt:) / .peerConnected(identity:) / .peerDisconnected(identity:reason:) / .error(SyncError)
- :126 `enum SyncState`: .disabled | .enabled(zone:lastPushAt:lastPullAt:) | .syncing(direction:) | .error(_:retryAt:)
- :134 `enum SyncError: Error, Equatable`: notEnabled/alreadyEnabled/schemaMismatch/kitMismatch/transportFailure/decodingFailure/encodingFailure/peerUnreachable/authenticationFailed/unsupportedTable/corruptRemoteIdentity(recordName:): last case: NEVER fabricate a UUID for an unparseable remote recordName, see CKRecordMapping.decode

### Wire format: SyncRecord.swift
- :28 `struct SyncRecord: Codable`: table/event/rowKey/values/hlc/schemaVersion/kitID; explicit CodingKeys: Rust serde renames match verbatim, do not let these drift
- :63 `enum SyncEventKind: String, Codable`: insert|update|delete; :68 `init(from: StorageEvent)`, :76 `.asStorageEvent`: Codable mirror, kept separate from PersistenceKit.StorageEvent deliberately
- :87 `struct PackedHLC: Codable, Hashable`: physicalTime/logicalCount/nodeID; Codable wrapper of SubstrateTypes.HLC
- :112 `struct SyncValueMap: Codable`: wraps [String:TypedValue] as [String:SyncValueBox]
- :144 `struct SyncValueBox`: kind + payload (13 TypedValue cases); adjacently-tagged JSON matching Rust `#[serde(tag="kind",content="payload")]`
- :206 `SyncValueBox: Codable` extension: :211 encode omits payload key for .null (matches Rust unit-variant omission); :227 timestamp encode guards `interval.isFinite` + Int64 range BEFORE `Int64(interval)` cast: Int64(_:) traps on NaN/±inf/overflow, corrupt inbound data can produce these
- :296 `struct FingerprintWire: Codable, Hashable`: block0..3 UInt64, Codable wrapper of SubstrateTypes.Fingerprint256

### None backend: ConvergenceKitNone.swift
- :28 `final class NoSyncEngine: SyncEngine`: passthrough; push/pull return .empty once enabled, throw .notEnabled before
- :53 `subscribe()`: stream that NEVER emits; closes only on caller task cancellation
- :64 `actor StateActor`: enable throws .alreadyEnabled on repeat; tracks manifest only for state reporting

### CloudKit mapping: CKRecordMapping.swift
- :26 `enum CKRecordMapping`: generic row↔CKRecord translator, driven by table name + manifest, no per-table hardcoding
- :31 `recordType(kitID:table:) -> String`: "\(kitID)_\(table)"
- :36 `recordID(rowKey:zone:) -> CKRecord.ID`
- :43 `record(from:table:rowKey:hlc:schemaVersion:kitID:zone:) throws -> CKRecord`: adds reserved _syncHLC/_syncSchemaVersion/_syncKitID fields
- :65 `decode(_:) throws -> DecodedRecord`: reverse; throws .decodingFailure if _syncHLC/_syncSchemaVersion missing; recovers table from "kitID_table" split; **throws .corruptRemoteIdentity if recordName is not a valid UUID: NEVER fabricate a fresh UUID here**
- :103 `assign(value:to:forKey:)` (private): TypedValue→CKRecord field; .array throws .encodingFailure (unsupported); .fingerprint packs 4×UInt64 LE into 32 bytes
- :147 `typedValue(from:)` (private): CKRecord field→TypedValue; NSNumber objCType sniffed for bool/float/int disambiguation
- :172 `packed(_:) -> Int64` / :179 `unpacked(_:) -> HLC`: HLC↔Int64, PINNED bit layout: 48 bits physical | 12 bits logical | 4 bits node: changing widths breaks previously-stored _syncHLC values
- :192 `struct SyncMeta`: hlc/schemaVersion/kitID extracted from _sync* fields
- :198 `struct DecodedRecord`: table/rowKey/values (clean, no _sync* keys)/syncMeta + convenience accessors

### CloudKit engine: CloudKitSyncEngine.swift
- :34 `final class CloudKitSyncEngine: SyncEngine`: :43 init(containerIdentifier:) defers CKContainer resolution to enable() so tests can construct without iCloud entitlement
- :78 `actor CloudKitStateActor`: owns container/manifest/storage/pendingOutbound/serverChangeToken/hlcGenerator
- :112 `enable(manifest:storage:)`: creates CK zone (tolerates already-exists); subscribes storage.observer.observe per push-eligible table → recordOutbound
- :106 `hlcGenerator = HLCGenerator(nodeID: Int32.random(in: 1...0x0F))`: mints HLC only when an observed change carries none
- :175 `push()`: drains pendingOutbound; builds CKRecords via CKRecordMapping.record; one modifyRecords(saving:deleting:) call; HLC: prefer change.hlc, else hlcGenerator.send(now:) (NOT currentTime(): send advances the logical counter, avoiding same-millisecond collisions)
- :254 `pull()`: recordZoneChanges(inZoneWith:since:); per-record gate: kitID match → schemaVersion match → table declared & not pushOnly; any failure caught+logged+counted as conflict, does NOT abort the batch; deletions are bare CKRecord.ID (no table info): attempted against every non-pushOnly manifest table
- :339 `applyInbound(_:syncedTable:storage:)` (internal, not private: LWW tests call via @testable): dispatches 4 ConflictPolicy arms; .lastWriterWinsByHLC reads existing row's _syncHLC as EITHER .hlc (InMemory) OR .int (SQLite/Postgres, raw packed value): must handle both

### Federation identity: FederationIdentity.swift
- :24 `struct PeerIdentity: Hashable`: 32-byte Ed25519 public key
- :32 `struct LocalIdentity`: :36 init() generates fresh Curve25519.Signing.PrivateKey per engine instance; :42 init(privateKeyBytes:) restores from caller bytes: module does NOT persist identity itself
- :48 `sign(_:) throws -> Data`
- :53 `enum FederationSignature`: :54 `verify(_:of:by:) -> Bool`: returns false (not throw) for both malformed pubkey bytes and bad signature

### Federation pairing shapes: HyperplaneFamilyExchange.swift
- :27 `struct HyperplaneFamilySpec: Codable, Hashable`: seed + dimension (default 256); seed alone reproduces the family deterministically on both sides
- :42 `struct PairingProposal: Codable`: proposerPublicKey/proposedFamily/nonce
- :54 `struct PairingAcceptance: Codable`: accepterPublicKey/acceptedFamily/signatureOfProposal
- NOTE: these types are defined but NOT YET wired into FederationSyncEngine.pair: v1.0 pair() takes an already-agreed HyperplaneFamilySpec directly, does not negotiate/sign Proposal/Acceptance

### Federation engine: FederationSyncEngine.swift
- :39 `final class FederationSyncEngine: SyncEngine`
- :80 `pair(with:via:family:) async throws`: exchanges pubkeys + family spec; in-process only at v1.0
- :84 `identity: LocalIdentity { get async }`
- :98 `enum PayloadKind: UInt8, Codable`: .syncRecordBatch = 0x01 (only v1.0 variant); 0x02 RESERVED for fieldWriteEventBatch: never reassign
- :128 `envelopeSigningBytes(senderPublicKey:payloadKind:payload:hlc:) -> Data`: PINNED byte layout (all LE): pubkey(32) | kind(1) | payload_len(4) | payload(N) | hlc.physicalTime(8) | hlc.logicalCount(4) | hlc.nodeID(4); byte-identical to Rust `envelope_signing_bytes` in federation.rs: signature covers THIS, not raw payload (closes relabel/replay seam)
- :174 `struct SignedEnvelope: Codable`: senderPublicKey/payloadKind/payload/signature/hlc; batch-level hlc minted AFTER all record HLCs in the batch (strictly later)
- :211 `protocol Relay: Sendable`: send(to:message:) / drain(for:): transport extension point; hosted relay is a drop-in conformer
- :220 `final class FederationRelay: Relay`: in-process only at v1.0, NSLock-protected [pubkey: [SignedEnvelope]] inbox dict
- :243 `actor FederationStateActor`: :244 `localIdentity = LocalIdentity()` generated once per actor
- :263 `enable(manifest:storage:)`: same observer-wiring pattern as CloudKitStateActor
- :279 `disable()`: cancels observer tasks THEN AWAITS each task's completion before clearing state (deliberately different from a cancel-only teardown: closes a race where a buffered change lands after disable() returns)
- :318 `pair(with:via:family:)`: registers peer locally, calls peerActor.acceptPeering symmetrically: one call registers both sides
- :331 `push()`: builds SyncRecord batch from pendingOutbound (HLC: change.hlc ?? hlcGenerator.send(now:), same rule as CloudKit), JSON-encodes, mints batch HLC, signs via envelopeSigningBytes, sends SignedEnvelope to every peer's relay
- :422 `pull()`: per envelope, in order: (1) senderPublicKey MUST equal the specific paired peer's key (ADR-013: valid self-signature alone does not prove pairing authorization), (2) payloadKind MUST be .syncRecordBatch, (3) signature MUST verify over envelopeSigningBytes, (4) only then JSON-decode; each surviving SyncRecord re-checked for kitID/schemaVersion/table-declared before applyInbound
- :526 `applyInbound(_:syncedTable:storage:)` (internal, not private: LWW tests): 4 ConflictPolicy arms × insert/update vs delete (delete gets full policy dispatch here, unlike CloudKit which receives bare record IDs with no policy routing): .appendOnly rejects remote deletes outright; .lastWriterWinsByHLC gates delete on stale-HLC check same as upsert path; .remoteWins deletes unconditionally; .localWins rejects all remote deletes

## INVARIANTS / GOTCHAS

- Application code NEVER calls a backend's push/pull for ordinary use: it writes to PersistenceKit; the enabled backend observes via StorageObserver and does the rest. Inbound changes apply through PersistenceKit's normal RowStore path (upsert/insert/delete), which is what wakes downstream StorageObserver watchers on the receiving side: do not special-case "just-synced" rows.
- CKRecordMapping.decode: an unparseable `recordID.recordName` MUST throw `.corruptRemoteIdentity`, never fabricate a fresh UUID. A fabricated identity creates a phantom row that desyncs forever (never matches the real remote row on later rounds).
- HLC bit layout in CKRecordMapping.packed/unpacked is PINNED: 48/12/4 bits (physical/logical/node). Changing widths invalidates every previously stored `_syncHLC` value.
- envelopeSigningBytes layout is PINNED and cross-port: pubkey(32)|kind(1)|payload_len(4 LE)|payload|hlc.physicalTime(8 LE)|hlc.logicalCount(4 LE)|hlc.nodeID(4 LE). Must stay byte-identical to Rust's `envelope_signing_bytes` in `rust/src/federation.rs`, or cross-port signatures fail verification.
- SyncRecord / SyncValueBox / PackedHLC / FingerprintWire CodingKeys are an explicit cross-port JSON contract (Rust serde field renames match these strings verbatim). Do not let automatic Codable key derivation replace these without checking the Rust side.
- PayloadKind 0x02 is RESERVED for `fieldWriteEventBatch`: never assign it to anything else; a receiver on an older build would silently misinterpret a reused byte value.
- Federation pull() enforces sender==paired-peer-key BEFORE trusting a valid signature (ADR-013): a syntactically valid self-signed envelope from a non-paired sender must be rejected, not merely a badly-signed one.
- `_syncHLC` on a stored row can arrive as TypedValue `.hlc` (InMemory backend, preserves the type) OR `.int` (SQLite/Postgres, raw packed integer, because those schemas don't declare the column as `.hlc`). Both `CloudKitStateActor.applyInbound` and `FederationStateActor.applyInbound` must handle both cases: do not assume one representation.
- lastWriterWinsByHLC gates BOTH upserts and deletes on HLC comparison in Federation's applyInbound; CloudKit's applyInbound only gates upserts this way because CloudKit deletes arrive as bare record IDs with no policy dispatch at all (deletes just happen, scoped only by "table is in the manifest and not pushOnly").
- FederationStateActor.disable() cancels observer tasks THEN awaits their completion before clearing state: do not reduce this to cancel-only; a buffered change could otherwise land in the outbox after disable() returns.
- HLC minting for locally-observed changes with no HLC of their own always uses `HLCGenerator.send(now:)`, never a read-only clock snapshot (`currentTime()` equivalent): `send` advances the logical counter so two changes minted in the same millisecond do not collide. Same rule in both CloudKitStateActor.push and FederationStateActor.push.
- Timestamp encoding in SyncValueBox guards `interval.isFinite` and Int64-range BEFORE casting to Int64: `Int64(_:)` traps (crashes) on NaN/±infinity/out-of-range Double. Corrupt inbound sync data can produce such a value; the guard converts a potential crash into a catchable EncodingError.
- Public API stability per README: adding a case to SyncDirection, ConflictPolicy, SyncEvent, SyncState, or SyncError is a breaking change requiring a major version bump and a decision record. Backend additions (a fourth SyncEngine conformer) are additive, not breaking.
- CloudKit backend has NO Rust port by design (Apple-only; Swift side always handles iCloud transport). Do not expect rust/ to mirror ConvergenceKitCloudKit.
- ConvergenceKit is intentionally NOT a dependency of other in-SDK kits at this commit (e.g., QueueKit's Package.swift excludes it per spec §11): it is wired in at the application-composition layer, not the kit-graph layer.
