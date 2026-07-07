---
doc: DETAILS
package: QueueKit
repo: moot-system
authored_commit: 3c3ce06528a1d1b3b6e9aa8a6008cba20a243c23
authored_date: 2026-07-07
sources:
  - path: Sources/QueueKit/DrainLease.swift
    blob: 3f1df6aa1fab44c993bd49d96de179ff03450303
  - path: Sources/QueueKit/FilesystemBackend.swift
    blob: c0e5f536e20b1def11875e57b587e8392b6f936d
  - path: Sources/QueueKit/Job.swift
    blob: 5f39e97a8113e47de2f2a18563446072de8ec6cf
  - path: Sources/QueueKit/ObservationStatus.swift
    blob: a32b0e8c93311ce498d12ffb4df864a2652c707e
  - path: Sources/QueueKit/PersistenceKitBackend.swift
    blob: 96175a8b27c8b6e929f417d95b141f727c5179d7
  - path: Sources/QueueKit/QueueBackend.swift
    blob: eb67e0b821bc9bfdec8cfde621c5e70d4295d331
  - path: Sources/QueueKit/QueueError.swift
    blob: 2697c4b7404b9e04259267cd8f4008030ebb6754
  - path: Sources/QueueKit/QueueKit.swift
    blob: 3878243f6da8ad1b55bba5271f7502e8d6b8e3d7
  - path: Sources/QueueKit/QueueKitTelemetry.swift
    blob: 7b117578b25d9e4c2df18f4a2835d987a4a893f7
  - path: Sources/QueueKit/Watcher.swift
    blob: cf2b270b9c60da34f7a25c016f8c18b6ba6149e4
---

# QueueKit Details

## Current Release Details

`QueueLatencyWindowBox` now guards the latency window and throttle state together.
Sampling and throttle checks happen under one lock.
Every drain tick still updates the rolling window.
Only the emission step is rate-limited.
The default interval is 30 seconds per estate stream.

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline
order. The wire-format types come first. The error vocabulary comes next,
followed by the backend contract and the public facade. Then come the two
concrete backends. Last comes the supporting infrastructure: the drain
lease, the directory watcher, and telemetry.

## Job.swift

This file provides the wire-format types every job and completion signal
is built from: `JobID`, `StreamID`, `SessionID`, `ToolName`, `ArtifactRef`,
`CodableValue`, `Job` itself, `MissionContext`, `WireFormat`, and
`SignalFile`.

A job needs an identifier that is both unique and safe to use as a
filename. The filesystem backend uses this identifier directly in a path.
`JobID.generate()` builds one from a UUID, a 128-bit random identifier. It
renders the UUID as thirty-two lowercase hexadecimal characters, with the
hyphens removed. This removal matters because QueueKit already uses a
hyphen as a separator inside maildir filenames, and the UUID's own hyphens
would otherwise collide with it.

`StreamID` names the workload a job belongs to. Examples are "encode" or
"dreaming." Jobs on different streams can share one queue. A consumer of
one stream never sees another stream's jobs.

`SessionID.mint()` stamps one drain call. Every job a single `drain()`
claims shares one session identifier. This is what lets a caller retire a
whole claimed batch in one step later.

`ArtifactRef` names something a completed job produced. It can name a
file, a commit, or a signal file. It can also name a step in a recorded
trajectory. An explicit `type` field on the wire tags which of the four
cases applies, so a decoder can tell them apart.

`CodableValue` is a caller-defined extensions blob. It is an open-ended
value. It can be a string, a number, a boolean, null, an array, or a
nested object. It exists so a job's `extensions` dictionary can carry
arbitrary structured data supplied by whatever produced the job. Every
field of that data survives a `send()`/`drain()` round trip unchanged.
QueueKit does not need to know the shape of that data in advance.

`Job` is the unit of work. It carries an identifier, a stream, an HLC
submission timestamp, and a priority. It also carries a payload of raw
bytes and the extensions dictionary. Its custom `Codable` conformance is
not decorative. It implements the exact wire format the package
specification fixes. Keys use `snake_case`, so the wire uses `stream_id`
rather than `streamID`. The HLC nests as `physical_time`, `logical_count`,
and `node_id`. The payload is base64url text, rather than raw JSON bytes.

This wire format is a cross-language contract. The Rust and Python ports
must produce and consume the identical bytes. Every field name, nesting
shape, and encoding choice here is pinned. None of it is a matter of Swift
convention.

`MissionContext` is the one type in this file that uses ordinary Swift
`Codable` key synthesis. It uses `camelCase` property names, and it needs
no custom coding keys. This type does not use the pinned wire format,
because it is not part of the job envelope. It is instead a
caller-supplied payload shape, with no cross-language byte-identity
requirement.

`WireFormat.filename(for:)` builds the canonical maildir filename for a
job. The filename joins three parts by hyphens: a sortable HLC string, the
stream identifier, and the job identifier.

`WireFormat.sortableHLC(_:)` is why the filename sorts correctly by claim
order. It zero-pads each HLC component to a fixed width: sixteen digits
for physical time, eight for the logical count, and ten for the node
identifier. This padding makes ordinary lexicographic string sorting match
HLC's own ordering. Lexicographic sorting is the only sort a plain
directory listing gives a caller.

`WireFormat.encoder` fixes sorted keys and no escaped slashes. The exact
same job therefore encodes to the exact same bytes, on every run and every
platform. This is what makes the shared conformance fixtures meaningful.

`SignalFile` is the durable record a completed job leaves behind. It
records four things. It records which job finished. It records what
terminal status resulted. It records what artifacts the job produced. It
records when the job finished. Its `Codable` conformance mirrors `Job`'s.
It uses the same pinned `snake_case` keys and the same nested HLC. This
consistency matters because whichever consumer or tool eventually inspects
the `done/` directory reads a signal file, regardless of which language
that consumer is written in.

## ObservationStatus.swift

This file provides `ObservationStatus`, the small enumeration a job's
outcome is reported in. Its cases are `.running`, `.done`,
`.doneWithConcerns`, `.needsContext`, and `.blocked`.

The type is owned here rather than duplicated. More than one package needs
to agree on what a finished job's outcome can be. The file's header
comment names AgentHarness as one such package. AgentHarness imports the
type directly from QueueKit, rather than declaring its own copy.

`isTerminal` is the one piece of logic on the type. Only `.running` is
non-terminal. `reply(to:status:)` on the `QueueKit` facade checks
`isTerminal` before accepting a completion. This check stops a caller from
accidentally marking a job finished with the one status that means it has
not finished.

## QueueError.swift

This file provides `QueueError`, the complete error vocabulary a caller of
QueueKit can receive.

Each case names one specific failure. A directory could not be created. A
write failed. A rename failed. A job's stored bytes could not be decoded.
A job identifier could not be found. The watcher failed. A stale
temporary file was found. The backend was unavailable. A caller tried to
complete a job with a non-terminal status. `awaitDrain` timed out. A
caller-supplied identifier was unsafe to use in a path.

Every distinct failure gets its own case, rather than a single generic
wrapped error. This design lets a caller handle a `jobNotFound`
differently from a `renameFailed`, without inspecting error text.

`drainTimeout(pending:inFlight:)` and `invalidIdentifier(id:reason:)` each
carry extra diagnostic detail. `drainTimeout` explains how far the queue
was from empty when `awaitDrain` gave up. `invalidIdentifier` explains
exactly why a submitted identifier was rejected.

## QueueBackend.swift

This file provides `QueueBackend`, the protocol every concrete backend
conforms to. It also provides two protocol extensions. These extensions
supply default implementations for the operations a backend does not have
to implement specially.

The protocol is the seam that lets the public `QueueKit` class stay
ignorant of which storage mechanism sits underneath it. Every method the
facade exposes has a matching method here. `QueueKit` does nothing but
forward to whichever `QueueBackend` it was mounted with. Six core
operations must be implemented from scratch by every backend: `write(_:)`,
`drainAvailable()`, `watch(handler:)`, `complete(_:status:artifacts:)`,
`inFlight()`, and `completed(streamID:)`.

`writeBatch(_:)` and `completeBatch(_:)` are bulk twins of `write` and
`complete`. The first default extension implements them by simply looping
the single-item version. This loop is correct for any backend, but it is
not necessarily fast. A backend earns the right to skip the loop by
overriding these two methods. The override must use a mechanism that pays
a shared cost once for the whole batch, instead of once per job. Both
concrete backends do exactly that, for reasons explained under their own
headings below.

`drainAvailable(stream:)` and `pendingCount(stream:)` are the
stream-scoped forms of `drainAvailable()` and `pendingCount()`. The second
protocol extension gives `drainAvailable(stream:)` a default
implementation. This default claims every stream's jobs through the
all-streams `drainAvailable()`, then filters the result down to the
requested stream. The result is correct, but it claims jobs belonging to
other streams, and those jobs then have to be re-enqueued. The file's own
comment warns that a concrete backend should override this default,
rather than rely on it. Both `FilesystemBackend` and `PersistenceKitBackend`
override it, each in the way suited to its own storage.

## QueueKit.swift

This file provides the public facade. The facade is the `QueueKit` class
itself, its maildir directory-management helpers, and the four permanent
methods every caller uses. Those four methods are `send`, `drain`,
`watch`, and `reply`. Every caller uses them the same way, regardless of
which backend is mounted underneath.

`QueueKit` holds four properties. It holds a `backend`, anything
conforming to `QueueBackend`. It holds an optional `root` URL, present
only when the mounted backend is a filesystem backend. It holds a
latency window for telemetry, wrapped in `QueueLatencyWindowBox` for
thread safety. Two streams can drain the same queue at once. Their
reports must not corrupt the shared sample list. It holds an
`estateTag` string, used to tag telemetry metrics by estate.

Two initializers exist. `init(root:hlcGenerator:)` is the common path. It
builds a `FilesystemBackend` at the given root, and it cleans any stale
temporary files left behind by a prior crash. `init(backend:root:)` mounts
an already-constructed backend directly. This is how `PersistenceKitBackend`
gets attached. It is also how tests substitute an in-memory backend.

`send(_:)` and its batch twin `send(batch:)` hand jobs to the backend. The
filesystem backend's per-job `write` pays a filesystem synchronization
cost on every single job. This cost is a call that forces the operating
system to guarantee the write has actually reached durable storage. The
batch form exists to avoid paying that cost per job. A bulk producer,
enqueuing tens of thousands of jobs at once during a full reindex, can
instead route through `writeBatch`. The filesystem backend implements
`writeBatch` as one durability barrier for the whole batch, rather than
one barrier per job.

`drain()` and the stream-scoped `drain(stream:)` claim available jobs.
Around the backend call, each method measures how long the claim took,
and reports that duration through `reportQueueStats`. Both return the
same shape: a list of pairs. Each pair holds a claimed `Job` and the
`SessionID` that drain call minted.

`watch(handler:)` subscribes a handler. The handler is invoked for every
job as it becomes available. The facade does nothing but forward to the
backend's own `watch`. The two backends drive this very differently. The
filesystem backend uses a directory watcher. The PersistenceKit backend
uses a database change observer.

`reply(to:status:artifacts:)` is the single-job completion path. It
checks `status.isTerminal` before forwarding to the backend. This check
stops a caller from marking a job complete with the one status,
`.running`, that means the job has not finished.

`reply(session:status:)` completes every job claimed under one drain
call's session, in a single pass. It has a real fast path only on
`PersistenceKitBackend`, checked with an `as?` cast. Only that backend's
shared table can look up an entire session's claimed rows in one guarded
update. On any other backend, `reply(session:status:)` returns `0`. That
return value signals the caller to fall back to per-job `reply`.

`reply(batch:)` completes an explicit list of job and status pairs, in one
pass. It routes to the backend's `completeBatch`. This is the path a
corpus-drain caller uses on the filesystem backend, which has no session
fast path but does have a batched completion.

`reclaimInFlight(stream:)` resets every job stuck in-flight for one stream
back to pending. The next `drain(stream:)` call then re-claims and
re-drives it. The doc comment states a strict precondition. A caller must
call this method only immediately after successfully acquiring that
stream's `DrainLease`. A freshly acquired lease guarantees the job's prior
claimant is actually gone, rather than merely slow. Without that
guarantee, this method could yank a job out from under a drain worker
still processing it.

Like `reply(session:status:)`, `reclaimInFlight(stream:)` has a real fast
path only on `PersistenceKitBackend`. `FilesystemBackend` instead exposes
an unscoped `reclaimInFlight()` that runs once at mount. Its `cur/`
directory has no stream separation in the first place, so no scoping is
possible there.

`inFlight()`, `pendingCount()`, and `pendingCount(stream:)` are read-only
depth probes. `inFlight()` reports how many jobs are claimed but not yet
replied to. `pendingCount()` reports how many jobs are waiting to be
claimed. Adding `pendingCount()` to `inFlight().count` gives the total
outstanding work a drain still has left to do. A status reader can
compute this total without claiming or draining anything.

`awaitDrain(pollInterval:timeout:)` and its stream-scoped twin
`awaitDrain(stream:pollInterval:timeout:)` block until a queue, or one
stream of it, is empty on both frontiers. Nothing may be pending, and
nothing may be in-flight. Both methods are polling loops, rather than
push-based latches. The maildir backend has no native event for "the
queue just emptied," so there is nothing to wait on directly. Each
iteration re-reads the live counts. Progress made by a concurrently
running drain worker between polls is therefore observed on the very next
tick.

Both methods return promptly when the queue is already empty. The
first check can succeed without the method ever sleeping. The timeout
measures time without progress. It does not cap the total wait. Each
method tracks the lowest outstanding count seen so far. Every poll
that sees a lower count resets the deadline. A drain that keeps
shrinking the queue, even slowly, never times out. Only a stalled
drain, with no frontier movement for the full timeout, throws
`QueueError.drainTimeout`. A stuck or crashed drain worker therefore
surfaces as an error, instead of as a hang.

`ensureMaildir(root:)` and `cleanStaleTmpFiles(root:)` are the maildir
directory-management helpers that `FilesystemBackend`'s initializer calls.
`ensureMaildir(root:)` creates four subdirectories if they are absent:
`tmp`, `new`, `cur`, and `done`. `cleanStaleTmpFiles(root:)` deletes
any file in `tmp/` older than `staleTmpThreshold`, five minutes.

A file stranded in `tmp/` means a process crashed between writing it and
renaming it into `new/`. Because the file was never visible in `new/`, no
consumer could have claimed it. Removing it on the next mount is
therefore safe.

## FilesystemBackend.swift

This file provides `FilesystemBackend`, the POSIX maildir-style queue
implementation. It uses no database, just four directories and atomic
filesystem operations. Its semantics are deliberately derived from
`deliver_maildir()`, the mail-delivery routine at the heart of the Postfix
mail server. This routine is a decades-proven pattern. It hands off many
small files between uncoordinated processes, without a lock file.

`validateIdentifier(_:)` runs before any filesystem path is built from a
caller-supplied identifier, whether a stream identifier or a job
identifier. It rejects an empty string. It rejects a `.` or `..` path
component. It rejects a `/` or `\` path separator. It rejects any ASCII
control character. Without this check, a maliciously or accidentally
crafted stream identifier containing a path separator could make a job's
path resolve outside the queue's root directory. That outcome is a
path-traversal risk. The Rust and Python ports enforce the identical rule.

`write(_:)` implements the maildir handoff in three classic steps. First,
it creates the job's encoded bytes in `tmp/`, with a mode that only the
owning process can read. This restricted mode is defense in depth, since
a queued payload may carry sensitive estate content. Second, it forces
those bytes to durable storage. Third, it atomically renames the file
into `new/`.

`atomicWriteAndRename(...)` is where those steps actually happen. It opens
the file with `O_CREAT | O_EXCL`, an exclusive create only one caller can
win. It then runs a raw `write` and `fsync` sequence, followed by
`rename`. It finishes with a final `fsync` of the destination directory
itself. This last step matters because a directory's own metadata is
separate durable state from the file contents, on POSIX filesystems. The
metadata records which files the directory currently contains. Both the
metadata and the file contents must be forced to disk, for the write to
survive a crash. A `rename` failure caused by the destination temporarily
vanishing, an `ENOENT` error, is retried once. Another process cleaning
`new/` mid-flight is a recoverable race, rather than a fatal one.

`writeBatch(_:)` is the bulk twin. It writes and renames every job in the
batch using `writeAndRenameNoFsync`, which skips the per-file durability
step. It forces only `new/`'s directory metadata to disk once, at the
end.

A crash before that single barrier can lose some just-written jobs. The
comment records why this is an acceptable trade for a bulk producer
specifically. The reindex that calls `writeBatch` derives its jobs from
the estate itself, so a lost job is simply re-enqueued on the next resume.
This is an at-least-once guarantee. It is not a promise that every job
survives every possible crash instant. Ordinary streaming `write` keeps
its per-job durability unchanged. Only a caller that opts into the batch
API accepts this weaker bound.

`pendingCount()` counts the files in `new/`. One file means one pending
job, so no decoding is required. This is why the count stays cheap even
as a queue grows.

`drainAvailable(stream:)` is the stream-scoped claim. The maildir filename
already encodes the stream, in the form `{hlc}-{stream}-{jobid}`. A naive
implementation could filter by filename alone. The code instead reads and
decodes each file's actual `streamID` field before claiming it. Files
whose stream does not match are left entirely untouched in `new/`. A
concurrent drainer for a different stream is never affected by this one's
scan. `pendingCount(stream:)` performs the equivalent non-claiming count.

`drainAvailable()`, the all-streams claim, renames every file in `new/`
into `cur/` first. Only then does it read and decode each one. A file
that fails to decode is moved straight to `done/`, rather than left to
jam every future drain pass. A job whose stored bytes are unparseable can
never become processable by simply trying again.

`reclaimInFlight()` moves everything left in `cur/` back to `new/`. Its
doc comment states its precondition plainly. A caller should call it only
at mount, before any drain session is live. A freshly started process
cannot have claimed anything itself. Anything already sitting in `cur/`
must therefore be a crash orphan from whichever process ran before it.

`watch(handler:)` wires `Watcher.watchNewDirectory` to `drainAvailable()`.
Every time the watcher signals that `new/` may have changed, the backend
drains whatever is currently claimable, and hands each job to the
caller's handler.

`complete(_:status:artifacts:)` finds the job's file in `cur/` by
matching the filename suffix. Every maildir filename ends in `-{jobID}`.
The method writes the `SignalFile` recording the outcome, and only then
moves the job's own file into `done/`. Writing the signal first matters.
If the process crashes between the two steps, the job is still visible
in `cur/` on the next mount, ready to be reclaimed. Without this order,
the job could vanish silently, leaving an orphaned signal and no job
record.

`completeBatch(_:)` is the batched form. One scan of `cur/` builds a
job-identifier-to-filename index. Every completion in the batch is
written without a per-file durability step. One call forces `done/`'s
directory metadata to disk at the very end. The safety argument mirrors
`writeBatch`'s argument. A crash before that final barrier leaves the
not-yet-moved jobs in `cur/`, where they are reclaimed and reprocessed.
This is safe because ingest is idempotent, and processing the same job
twice causes no harm.

`inFlight()` and `completed(streamID:)` both delegate to the private
`listJobs(in:filter:)`. This method lists a directory and skips
`.signal` sidecar files. It decodes each remaining file as a `Job`. It
can optionally filter by stream.

## PersistenceKitBackend.swift

This file provides `PersistenceKitBackend`, the second concrete backend.
Jobs live as rows in a table inside PersistenceKit, MOOTx01's storage
kit, rather than as files on disk. The file's own header comment states
five invariants the implementation is built to preserve. The sections
below explain each one, as it is enforced in code.

`QueueKitSchema.declaration()` defines the table's shape. There is one
row per job. Its columns hold the job's own fields, plus queue
bookkeeping. The bookkeeping columns are `status`, `signal_status`,
`artifacts`, and `session_id`. `status` is `"new"`, `"cur"`, or `"done"`.
`signal_status` and `artifacts` are filled in only once a job completes.
`session_id` names the drain call, if any, currently holding the row.

Three indices back the operations that matter. One index backs a plain
status count. A second index, on `(status, physical_time, logical_count,
node_id)`, backs claiming jobs in HLC order. A third index, on
`(stream_id, status)`, backs the stream-scoped operations. The table's
`appendOnly` flag is explicitly `false`. This is invariant 5. A queue row
must be mutable, since the same row moves from `new` to `cur` to `done`
in place.

`write(_:)` is a bare insert into the table, with `status: "new"`. It has
no enclosing transaction, per invariant 1. A transaction would offer no
benefit here, since there is nothing else to make atomic with a single
insert. A transaction would only add overhead to the hottest, most
frequent operation in the backend.

`writeBatch(_:)` wraps every insert in the batch in one `.readCommitted`
transaction. This transaction is the isolation equivalent of the
filesystem backend's single durability barrier. The comment explains why
the weaker `.readCommitted` isolation is still correct here, rather than
the `.serializable` isolation the claim uses. These are all inserts of
brand-new rows. Brand-new rows cannot conflict with the claim's job of
flipping existing `new` rows to `cur`. A claim that begins before the
batch commits simply will not see the batch's rows yet. It will see them
on its next pass instead.

`pendingCount()` and `pendingCount(stream:)` are single-row-count reads.
Both use a `status = 'new'` predicate. The stream form adds a
`stream_id` predicate. Neither read claims anything or locks beyond an
ordinary read.

`drainAvailable(stream:)` and the all-streams `drainAvailable()` both
implement the claim as one `.serializable` transaction. That transaction
has exactly two steps. First, an `UPDATE` flips every matching `new` row
to `cur`, under one freshly minted session identifier. Second, a `SELECT`
reads back precisely the rows carrying that session identifier, ordered
by HLC. This single-pass shape is invariant 3, the status-guarded atomic
transition. The file's comment calls it out as a deliberate improvement
over an earlier design, one that issued one guarded update per row.
Claiming a batch of jobs that older way cost one predicate scan per job,
so a bulk claim over a large queue became quadratic in the number of
jobs. Reading back by this call's own unique session, rather than by
`status = 'cur'` generally, keeps two concurrent drainers from ever
double-counting the same claimed row. Each call's session tags only the
rows it itself just claimed.

`completeSession(_:status:)` completes every row claimed under one
session, in a single guarded update. It is the batch twin of the per-job
`complete`. It is the reason `QueueKit.reply(session:status:)` has a fast
path only on this backend. The method is declared directly on the class,
rather than as part of the `QueueBackend` protocol. Only the concrete
`PersistenceKitBackend` handle has anything meaningful to complete a
whole session against. The abstract protocol does not.

`watch(handler:)` subscribes to `storage.observer`, per invariant 2. It
treats every event strictly as a wake signal. It never reads a job's
fields out of the event payload itself, only out of a subsequent
`drainAvailable()` call. `drainUntilEmpty` is the loop this drives.
Rather than draining once per event, it drains repeatedly until a pass
claims nothing. A burst of inserts can coalesce into fewer observer
events than there are actual rows, so draining only once per event would
strand whichever rows' wake got folded away. The initial drain, run
before the subscription's first `await`, matters for the same reason in
reverse. A job inserted between mount and subscription would otherwise
be invisible to any wake, since its insert event predates the
subscription.

`complete(_:status:artifacts:)` is a single guarded update, keyed on both
the job identifier and `status = 'cur'`. If nothing matched, the job was
never claimed, or it was already completed. The method throws
`QueueError.jobNotFound` in that case, rather than silently doing
nothing.

`reclaimInFlight(stream:)` resets a stream's `cur` rows back to `new`,
and clears `session_id`. Its doc comment repeats the same safety argument
as the facade method it backs. Calling it is safe only immediately after
successfully acquiring the stream's `DrainLease`. The lease's staleness
gate is what rules out a live drainer still holding those rows.

`inFlight()` and `completed(streamID:)` both route through the private
`listJobs(status:streamID:)`. `decodeRow(_:)` turns one database row back
into a `Job`. If the stored JSON for a row's extensions fails to parse,
`decodeRow(_:)` falls back to an empty extensions dictionary, rather than
failing the whole read.

## DrainLease.swift

This file provides `DrainLease`, a heartbeat-based, stream-keyed lock. It
lets many processes share one estate's queue, while guaranteeing that
only one of them actively drains a given stream at a time. Two different
streams get entirely independent leases, and both can be held at once.
This is what lets an encode drainer and a dreaming drainer run at the
same time, for example, without either blocking on the other.

The design deliberately avoids checking whether the prior holder's
process is still alive. A PID-liveness check would need OS-specific code,
querying the process table through platform-specific system calls. The
file's header comment rules this out. It favors something that works
identically on macOS, Linux, and Windows instead: a lease file holding an
owner string and a wall-clock timestamp.

`tryAcquire(now:)` succeeds in three cases. It succeeds when the lease
file is absent. It succeeds when the lease is already owned by the
caller. It succeeds when the lease's timestamp is older than `ttl`,
fifteen seconds. In every other case, another drainer holds a fresh
lease, and the caller must stand down. The owner string itself combines
the process identifier with a caller-supplied nonce. This combination
matters because the operating system can reuse a process identifier
after a crash. Without the nonce, a reused identifier could be mistaken
for the original holder.

`heartbeat(now:)` refreshes the timestamp while a drainer actively holds
the lease. The doc comment directs a caller to invoke it roughly every
five seconds, well inside the fifteen-second TTL. This cadence keeps an
ordinary slow drain pass from ever having its own lease expire out from
under it. `isHeldByOther(now:)` answers the same freshness question as
`tryAcquire`, without attempting to take the lease. A caller that only
wants to check before deciding whether to try uses this method instead.
`release()` deletes the lease file on clean shutdown, but only if the
caller still owns it. This guard means a takeover that already happened
is never undone by a late release from the prior holder.

The worst-case cost of a crashed holder is one full TTL. A takeover
cannot happen sooner than fifteen seconds after the last heartbeat,
because a fresher lease has to be honestly waited out. This is the trade
the design accepts, for staying free of any OS-specific liveness check.

## Watcher.swift

This file provides `Watcher`, the directory-change wake source
`FilesystemBackend.watch()` is built on. Its job is narrow, and its
contract is explicit. It calls `onChange` whenever the watched
directory's contents may have changed. Spurious wakes are allowed,
because `drainAvailable()` is always the actual authority on what can be
claimed. A false-positive wake costs one wasted scan. It is never a
correctness problem.

Three platform strategies exist, tried in order of preference.
`watchKQueue` runs only on Darwin platforms, meaning macOS and iOS. It
opens the directory with `O_EVTONLY`. It attaches a `DispatchSource`
watching kqueue VNODE
events: write, extend, delete, and rename. This strategy gives wake
latency in the microseconds, with no external dependency.

`watchLinux` first attempts `openInotify`. `openInotify` sets up an
inotify watch for `IN_CREATE` and `IN_MOVED_TO`. The second of these is
the event the atomic tmp-to-new rename actually fires. `runInotifyLoop`
then polls that file descriptor with a five-hundred-millisecond timeout,
so that task cancellation is still checked promptly between events. It
reads and discards whatever event bytes arrive. The handler only needs to
know that something happened. It does not need to know what happened. If
`openInotify` cannot establish a watch, because the kernel's inotify
watch limit is exhausted or the filesystem does not support inotify,
`watchLinux` falls back automatically to `watchPoll`.

Every other platform uses `watchPoll` directly. `watchPoll` is a plain
two-hundred-millisecond snapshot comparison of the directory's file
listing. It is correct everywhere, but it can miss a change for up to one
poll interval, about one hundred milliseconds on average.

Every path calls `onChange` once immediately, before entering its
platform-specific wait loop. This first call means any work already
present when the watcher attaches is not stranded waiting for the next
actual filesystem event. `ContinuationBox` is the small bridge the Darwin
path uses. It lets an `async` caller wait on a `DispatchSource`'s cancel
handler. That handler fires on an ordinary `DispatchQueue` callback,
rather than inside Swift's structured concurrency.

## QueueKitTelemetry.swift

This file provides `QueueLatencyWindow`, `QueueLatencyWindowBox`, and
`reportQueueStats`. These are the self-report telemetry QueueKit emits
through IntellectusLib, MOOTx01's telemetry library, after every
`drain()` call.

The reporting path is additive, and it is deliberately cheap when
unused. The very first check inside `reportQueueStats` is a single
atomic flag read, `Intellectus.isEnabled`. The function returns
immediately when that flag is false. The file's header comment puts the
cost of this check at about one nanosecond. When telemetry is enabled,
the function makes one `pendingCount()` call. It then emits up to six
named metrics. Every metric is tagged with the estate identifier and the
kit name, so a fleet of estates can be compared metric by metric.

`QueueLatencyWindow` keeps a fixed-capacity rolling list of recent drain
latencies, one hundred samples by default. It computes an arbitrary
percentile from that list on demand. `percentile(_:)` guards its input
explicitly. A percentile argument that is not a finite number between
zero and one hundred, for example `NaN` or a value produced by a
caller's arithmetic error, returns `0` rather than computing an array
index from it. Converting a non-finite floating-point value to an
integer index can crash the program outright on some inputs.

`QueueLatencyWindowBox` wraps `QueueLatencyWindow` in a lock. Two
drainers can share one `QueueKit` instance. An encode worker and an
import worker can each drain the same per-estate queue at once.
Without a lock, two concurrent appends could corrupt the sample
array's storage. `reportQueueStats` now takes the box, not the raw
window. It calls `sample(_:)` once per drain call. `sample(_:)`
appends the new latency sample. It also reads both percentiles under
one lock acquisition. No two drains can interleave between the append
and the read this way.

`reportQueueStats` treats a failed `pendingCount()` read as its own
signal, rather than as a zero. Reporting `queue.depth = 0` when the read
actually failed would be indistinguishable from an honestly empty queue.
It would tell whoever is watching the telemetry that everything is
drained, when the truth is that the read itself did not work. Instead, a
failed read skips the `queue.depth` metric entirely, in favor of a
dedicated `queue.depth_unavailable` counter. Every metric that can only
be computed honestly from a known depth is skipped along with it.
`queue.idle_nonempty` is one such metric, as is part of
`queue.head_of_line_age_s`. `queue.drain_count` and the two latency
percentiles have no such dependency, and they are always reported.

## Rust and Python Ports and Conformance

The `rust/` directory contains a second, independent implementation of
the package. It has eight source files: `error.rs`, `job.rs`,
`backend.rs`, `filesystem.rs`, `facade.rs`, `drain_lease.rs`,
`persistencekit.rs`, and `lib.rs`. A `persistencekit` feature flag gates
`persistencekit.rs`. These files reimplement the facade, both backends,
and the drain lease, for non-Apple hosts.

The `python/queuekit/` directory contains a third implementation. It
covers only `FilesystemBackend`. The package specification restricts
Python to that one backend, since Python tooling has no need of the
PersistenceKit database path.

All three implementations are gated by the same conformance fixtures.
These fixtures live in `Tests/QueueKitTests/Fixtures/`, as recorded job
and completion-signal input and output pairs. Each fixture pins one
exact set of bytes. Rust's own `rust/tests/conformance.rs` loads the
identical fixture files the Swift test suite reads. It asserts that
Rust's encoder produces the same bytes Swift already produced. This
mechanism is what turns "the wire format is a contract" from an
assertion in this document into something a test suite actually checks.
Suppose a field name, a key order, or an encoding rule changes in any one
implementation. The shared fixtures catch the drift immediately. They
catch it in whichever implementation was not updated to match.
