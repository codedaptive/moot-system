---
doc: OVERVIEW
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

# ConvergenceKit Overview

## What This Library Does

ConvergenceKit copies the rows that PersistenceKit stores from one device or
one MOOTx01 estate to another. An estate is one user's complete memory store
in MOOTx01; estates can federate, which means separate estates share and
compare memories. PersistenceKit is the package that reads and writes rows on
one device. ConvergenceKit sits beside it and watches for changes, then ships
those changes somewhere else: another of the user's own devices through
Apple's CloudKit, or a different user's estate through a peer-to-peer
exchange the SDK calls federation.

Application code never calls ConvergenceKit's sync methods directly for
ordinary reads and writes. It writes to PersistenceKit as usual.
ConvergenceKit is configured once, alongside PersistenceKit, to observe that
same storage. From that point on, replication is a side effect of normal
writes rather than a task the application must remember to perform.

## The Problem It Solves

A memory captured on one device is useless on another device until it
travels there. Two hard problems stand between "captured here" and
"visible there."

The first problem is conflicting writes. If the same row changes on two
devices before they synchronize, something must decide which change wins.
ConvergenceKit answers this with a per-table conflict policy declared once,
in advance, by the application. The default policy, `lastWriterWinsByHLC`,
settles conflicts using a Hybrid Logical Clock (HLC): a timestamp that
combines a device's wall-clock reading with a small counter, so that two
devices can order their changes consistently even when their clocks are not
perfectly synchronized. Three other policies exist for cases where
last-writer-wins is wrong: `appendOnly` for audit logs that must never
overwrite an entry, `localWins` for data a device should never let a peer
overwrite, and `remoteWins` for the opposite case.

The second problem is trust. Federated sync crosses a perimeter between two
separate people's estates, so a receiving device must be able to prove that
an incoming batch of changes really came from the peer it paired with, and
was not altered in transit. ConvergenceKit answers this with Ed25519
signatures. Ed25519 is a public-key signature scheme: each estate generates
a private key it never shares and a public key it hands to peers during
pairing. Every batch of changes is signed with the sender's private key
before it leaves the device, and the receiver checks the signature with the
sender's public key before applying anything.

CloudKit sync does not need this second protection, because it stays inside
one person's Apple account, authenticated by Apple's own private database.
Federated sync crosses to a stranger's device, so it needs its own
authentication independent of any platform account system.

## How It Works

ConvergenceKit defines one protocol, `SyncEngine`, and three backends that
conform to it: `NoSyncEngine`, `CloudKitSyncEngine`, and
`FederationSyncEngine`. An application picks exactly one backend per
PersistenceKit instance. All three backends expose the same four operations,
so application code that calls them does not need to know which backend is
active.

`enable(manifest:storage:)` turns sync on. A `SyncManifest` is a declaration,
written once by the application, of which PersistenceKit tables to sync, in
which direction (`bidirectional`, `pushOnly`, or `pullOnly`), and under which
conflict policy. Enabling a backend also starts it watching PersistenceKit's
`StorageObserver` for local inserts, updates, and deletes on every table the
manifest lists as push-eligible.

`push()` sends pending local changes outward and returns a `SyncReceipt`
summarizing what moved. `pull()` fetches pending remote changes and applies
them through PersistenceKit's own write path, which is what makes the
receiving side work correctly: an applied row is not a special sync
artifact, it is an ordinary row, so anything already watching PersistenceKit
notices the change the normal way. `subscribe()` returns a live stream of
`SyncEvent` values (changes applied, pushes completed, peers connecting or
disconnecting, errors) for a user interface to display.

Every backend that crosses a real network boundary needs a wire format for a
changed row. `SyncRecord` is that format: a table name, an event kind, a row
key, the changed column values, an HLC, a schema version, and a kit
identifier. The schema version and kit identifier exist so a receiver can
reject a record it does not understand, rather than misapply it. `SyncRecord`
is Codable with explicit coding keys, because the SDK maintains a Rust port
of large parts of the system, and both ports must produce the same JSON
field names for the same data.

## How the Pieces Fit

Figure 1 shows the library's topology: the shared protocol and wire format
at the center, PersistenceKit on either end, and the three backends as
interchangeable implementations in between.

![Figure 1. Topology of ConvergenceKit](topology.svg)

*Figure 1. Topology of ConvergenceKit. Application code writes only to
PersistenceKit. Whichever backend is enabled observes those writes and ships
them outward; on the receiving side, applying an inbound change routes back
through PersistenceKit's normal write path. Dashed regions mark the three
interchangeable backends and the external systems (CloudKit's private
database, the federation relay) each one talks to.*

`NoSyncEngine` is the simplest backend: it accepts `enable()`, then returns
empty receipts from `push()` and `pull()` forever. It exists for
single-device deployments, development, and tests, where wiring in a real
backend would add cost without adding value.

`CloudKitSyncEngine` replicates rows through Apple's CloudKit private
database, one CloudKit zone per estate. `CKRecordMapping` is the piece that
makes this backend generic rather than hand-written per table: it converts
any PersistenceKit row into a `CKRecord` and back, driven entirely by the
table name and the manifest, so adding a new synced table never requires new
mapping code.

`FederationSyncEngine` replicates rows to a different user's estate. Two
engines "pair" by exchanging Ed25519 public keys and a shared
`HyperplaneFamilySpec`, a parameter that lets both sides compare certain
256-bit fingerprints (short fixed-size codes computed from content) on equal
terms after pairing. Once paired, every pushed batch of `SyncRecord` values
is wrapped in a `SignedEnvelope`, signed, and handed to a `Relay`. At v1.0,
the shipped `Relay` implementation, `FederationRelay`, is in-process only,
holding pending messages in memory for peers running in the same process.
This is enough to exercise the full protocol, including signature
verification and conflict resolution, in tests; a wire transport that
reaches a peer on a different machine is future work.

## What Ships in the Package

The package ships four Swift targets: `ConvergenceKit` (the protocol, the
wire format, and the shared types), `ConvergenceKitNone`,
`ConvergenceKitCloudKit`, and `ConvergenceKitFederation`. A `rust/` port
mirrors the core types, the wire format, and the None and Federation
backends; CloudKit is Apple-only and has no Rust equivalent by design, so
the Swift side always handles that transport. Every field name shared
between the two ports is pinned in code comments and exercised by tests on
both sides, because a receiver on one port must decode a record written by
the other exactly.
