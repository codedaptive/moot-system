---
doc: DETAILS
package: QueueKit
repo: moot-system
authored_commit: 909513d0a8ecb1e9e903af9f4d25b3e4f2528242
authored_date: 2026-07-04
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
    blob: 60dfaa1e8f92ec051810b50d8b9cadc47388c02f
  - path: Sources/QueueKit/QueueKitTelemetry.swift
    blob: 29024f112bf133012283205175aa336b8d80d7c9
  - path: Sources/QueueKit/Watcher.swift
    blob: cf2b270b9c60da34f7a25c016f8c18b6ba6149e4
---

# QueueKit Details

This document walks through every source file in the package. Read
`OVERVIEW.md` first for the big picture. Files appear here in pipeline
order: the wire-format types, the error vocabulary, the backend contract,
the public facade, the two concrete backends, and finally the supporting
infrastructure — the drain lease, the directory watcher, and telemetry.

## Job.swift

This file provides the wire-format types every job and completion signal is
built from: `JobID`, `StreamID`, `SessionID`, `ToolName`, `ArtifactRef`,
`CodableValue`, `Job` itself, `MissionContext`, `WireFormat`, and
`SignalFile`.

A job needs an identifier that is both unique and safe to use as a filename,
because the filesystem backend uses it directly in a path. `JobID.generate()`
builds one from a UUID (a 128-bit random identifier), rendered as thirty-two
lowercase hexadecimal characters with the hyphens removed — this avoids
the hyphen colliding with the hyphen QueueKit uses as a separator inside
maildir filenames. `StreamID` names the workload a job belongs to (for
example, "encode" or "dreaming"); jobs on different streams can share one
queue without a consumer of one stream ever seeing the other's jobs.
`SessionID.mint()` stamps one drain call: every job a single `drain()`
claims shares one session identifier, which is what lets a caller retire
a whole claimed batch in one step later.

`ArtifactRef` names something a completed job produced — a file, a commit,
a signal file, or a step in a recorded trajectory — tagged with an explicit
`type` field on the wire so a decoder can tell the four cases apart.
`CodableValue` is a caller-defined extensions blob: an open-ended value that
can be a string, a number, a boolean, null, an array, or a nested object.
It exists so a job's `extensions` dictionary can carry arbitrary structured
data supplied by whatever produced the job, and have every field of it
survive a `send()`/`drain()` round trip unchanged, without QueueKit having
to know the shape of that data in advance.

`Job` is the unit of work: an identifier, a stream, an HLC submission
timestamp, a priority, a payload of raw bytes, and the extensions
dictionary. Its custom `Codable` conformance is not decorative — it
implements the exact wire format the package specification fixes: keys are
`snake_case` (`stream_id`, not `streamID`), the HLC nests as
`physical_time`/`logical_count`/`node_id`, and the payload is base64url
text, not raw JSON bytes. This wire format is a cross-language contract: the
Rust and Python ports must produce and consume the identical bytes, so every
field name, nesting shape, and encoding choice here is pinned, not a matter
of Swift convention.

`MissionContext` is the one type in this file that uses ordinary Swift
`Codable` key synthesis (`camelCase` property names, no custom coding keys)
rather than the pinned wire format, because it is not part of the job
envelope; it is a caller-supplied payload shape that has no cross-language
byte-identity requirement.

`WireFormat.filename(for:)` builds the canonical maildir filename for a job:
a sortable HLC string, the stream identifier, and the job identifier,
joined by hyphens. `WireFormat.sortableHLC(_:)` is why the filename sorts
correctly by claim order: it zero-pads each HLC component to a fixed width
(sixteen digits for physical time, eight for the logical count, ten for the
node identifier) so that ordinary lexicographic string sorting — the only
sort a plain directory listing gives you — matches HLC's own ordering.
`WireFormat.encoder` fixes sorted keys and no escaped slashes, so the exact
same job encodes to the exact same bytes on every run and every platform;
this is what makes the shared conformance fixtures meaningful.

`SignalFile` is the durable record a completed job leaves behind: which job,
what terminal status it ended in, what artifacts it produced, and when. Its
`Codable` conformance mirrors `Job`'s: pinned `snake_case` keys and a nested
HLC, because a signal file is read by whichever consumer or tool eventually
inspects the `done/` directory, in whichever language that consumer happens
to be written in.

## ObservationStatus.swift

This file provides `ObservationStatus`, the small enumeration a job's
outcome is reported in: `.running`, `.done`, `.doneWithConcerns`,
`.needsContext`, or `.blocked`.

The type is owned here, not duplicated, because more than one package needs
to agree on what a finished job's outcome can be; AgentHarness, mentioned in
the file's header comment, imports it directly from QueueKit rather than
declaring its own copy. `isTerminal` is the one piece of logic on the type:
only `.running` is non-terminal. `reply(to:status:)` on the `QueueKit`
facade checks this before accepting a completion, so a caller cannot
accidentally "finish" a job with the status that means it has not finished.

## QueueError.swift

This file provides `QueueError`, the complete error vocabulary a caller of
QueueKit can receive.

Each case names one specific failure: a directory could not be created, a
write failed, a rename failed, a job's stored bytes could not be decoded, a
job identifier could not be found, the watcher failed, a stale temporary
file was found, the backend is unavailable, a caller tried to complete a job
with a non-terminal status, `awaitDrain` timed out, or a caller-supplied
identifier was unsafe to use in a path. Keeping every distinct failure as
its own case, rather than a generic wrapped error, is what lets a caller
handle a `jobNotFound` differently from a `renameFailed` without inspecting
error text. `drainTimeout(pending:inFlight:)` and
`invalidIdentifier(id:reason:)` each carry the diagnostic detail a caller
needs to explain, respectively, how far the queue was from empty when
`awaitDrain` gave up, and exactly why a submitted identifier was rejected.

## QueueBackend.swift

This file provides `QueueBackend`, the protocol every concrete backend
conforms to, plus two protocol extensions supplying default
implementations for the operations a backend does not have to implement
specially.

The protocol is the seam that lets the public `QueueKit` class stay ignorant
of which storage mechanism is underneath it: every method the facade
exposes has a matching method here, and `QueueKit` does nothing but forward
to whichever `QueueBackend` it was mounted with. `write(_:)`,
`drainAvailable()`, `watch(handler:)`, `complete(_:status:artifacts:)`,
`inFlight()`, and `completed(streamID:)` are the core operations every
backend must implement from scratch.

`writeBatch(_:)` and `completeBatch(_:)` are bulk twins of `write` and
`complete`. The first default extension implements them by simply looping
the single-item version — correct for any backend, but not necessarily
fast. A backend earns the right to skip that loop by overriding these two
methods with a mechanism that pays a shared cost once for the whole batch
instead of once per job; both concrete backends do exactly that, for
reasons explained under their own headings below.

`drainAvailable(stream:)` and `pendingCount(stream:)` are the stream-scoped
forms of `drainAvailable()` and `pendingCount()`. The second protocol
extension's default implementation of `drainAvailable(stream:)` claims
every stream's jobs through the all-streams `drainAvailable()` and then
filters the result down to the requested stream — which is correct, but
claims jobs belonging to other streams that then have to be re-enqueued, so
the file's own comment warns that a concrete backend should override it
rather than rely on the default. Both `FilesystemBackend` and
`PersistenceKitBackend` do override it, each in the way suited to its own
storage.

## QueueKit.swift

This file provides the public facade: the `QueueKit` class itself, its
maildir directory-management helpers, and the four permanent methods —
`send`, `drain`, `watch`, `reply` — that every caller uses regardless of
which backend is mounted underneath.

`QueueKit` holds a `backend` (anything conforming to `QueueBackend`), an
optional `root` URL (present only when the mounted backend is a
filesystem backend), a rolling latency window for telemetry, and an
`estateTag` string used to tag telemetry metrics by estate. Two
initializers exist: `init(root:hlcGenerator:)` is the common path — it
builds a `FilesystemBackend` at the given root and cleans any stale
temporary files left behind by a prior crash. `init(backend:root:)` mounts
an already-constructed backend directly; this is how `PersistenceKitBackend`
gets attached, and how tests substitute an in-memory backend.

`send(_:)` and its batch twin `send(batch:)` hand jobs to the backend.
The batch form exists because the filesystem backend's per-job `write`
pays a filesystem synchronization cost — a call that forces the operating
system to guarantee the write has actually reached durable storage — on
every single job; a bulk producer enqueuing tens of thousands of jobs at
once (a full reindex, for instance) can instead route through
`writeBatch`, which the filesystem backend implements as one durability
barrier for the whole batch rather than one per job.

`drain()` and the stream-scoped `drain(stream:)` claim available jobs and,
around the backend call, measure how long the claim took and report it
through `reportQueueStats`. Both return the same shape: a list of pairs,
each a claimed `Job` and the `SessionID` that drain call minted.

`watch(handler:)` subscribes a handler that is invoked for every job as it
becomes available; the facade does nothing but forward to the backend's own
`watch`, because the two backends drive this very differently (a directory
watcher for the filesystem backend, a database change observer for the
PersistenceKit backend).

`reply(to:status:artifacts:)` is the single-job completion path; it checks
`status.isTerminal` before forwarding to the backend, so a caller cannot
mark a job "complete" with the one status (`.running`) that means it is
not. `reply(session:status:)` completes every job claimed under one drain
call's session in a single pass; it only has a fast path on
`PersistenceKitBackend` (checked with an `as?` cast), because only that
backend's shared table can look up an entire session's claimed rows in one
guarded update. On any other backend it returns `0`, signaling the caller
to fall back to per-job `reply`. `reply(batch:)` completes an explicit list
of job/status pairs in one pass, routing to the backend's `completeBatch`;
this is the path a corpus-drain caller uses on the filesystem backend, which
has no session fast path but does have a batched completion.

`reclaimInFlight(stream:)` resets every job stuck in-flight for one stream
back to pending, so the next `drain(stream:)` re-claims and re-drives it.
The doc comment is explicit about the precondition: call this only
immediately after successfully acquiring that stream's `DrainLease`, because
a freshly acquired lease is the guarantee that the job's prior claimant is
actually gone, not merely slow — without that guarantee, this method could
yank a job out from under a drain worker that is still processing it. Like
`reply(session:status:)`, it only has a fast path on `PersistenceKitBackend`;
`FilesystemBackend` instead exposes an unscoped `reclaimInFlight()` that
runs once at mount, because its `cur/` directory has no stream separation
in the first place.

`inFlight()`, `pendingCount()`, and `pendingCount(stream:)` are read-only
depth probes: how many jobs are claimed but not yet replied to, and how
many are waiting to be claimed. `pendingCount() + inFlight().count` is the
total outstanding work a drain still has left to do — a status reader can
compute that without claiming or draining anything.

`awaitDrain(pollInterval:timeout:)` and its stream-scoped twin
`awaitDrain(stream:pollInterval:timeout:)` block until a queue (or one
stream of it) is empty on both frontiers: nothing pending, nothing
in-flight. Both are polling loops, not push-based latches, because the
maildir backend has no native "the queue just emptied" event to wait on;
each iteration re-reads the live counts, so progress made by a concurrently
running drain worker between polls is observed on the very next tick. Both
return promptly when the queue is already empty — the first check can
succeed without ever sleeping — and both throw `QueueError.drainTimeout`
rather than blocking forever if the deadline passes with work still
outstanding, so a stuck or crashed drain worker surfaces as an error
instead of a hang.

`ensureMaildir(root:)` and `cleanStaleTmpFiles(root:)` are the maildir
directory-management helpers `FilesystemBackend`'s initializer calls: the
first creates the four subdirectories (`tmp`, `new`, `cur`, `done`) if
absent, and the second deletes any file in `tmp/` older than
`staleTmpThreshold` (five minutes). A file stranded in `tmp/` means a
process crashed between writing it and renaming it into `new/`; because it
was never visible in `new/`, no consumer could have claimed it, so removing
it on the next mount is safe.

## FilesystemBackend.swift

This file provides `FilesystemBackend`, the POSIX maildir-style queue
implementation: no database, just four directories and atomic filesystem
operations. Its semantics are deliberately derived from `deliver_maildir()`,
the mail-delivery routine at the heart of the Postfix mail server — a
decades-proven pattern for handing off many small files between
uncoordinated processes without a lock file.

`validateIdentifier(_:)` runs before any filesystem path is built from a
caller-supplied identifier (a stream identifier or a job identifier). It
rejects an empty string, a `.` or `..` path component, a `/` or `\` path
separator, and any ASCII control character. Without this check, a
maliciously or accidentally crafted stream identifier containing a path
separator could make a job's path resolve outside the queue's root
directory — a path-traversal risk. The Rust and Python ports enforce the
identical rule.

`write(_:)` implements the maildir handoff in the classic three steps:
create the job's encoded bytes in `tmp/` with a mode that only the owning
process can read (defense in depth, since a queued payload may carry
sensitive estate content), force those bytes to durable storage, then
atomically rename the file into `new/`. `atomicWriteAndRename(...)` is
where those steps actually happen, using `open` with `O_CREAT | O_EXCL` (an
exclusive create only one caller can win), then a raw `write`/`fsync`
sequence, then `rename`, then a final `fsync` of the destination
directory itself — an extra step needed because on POSIX filesystems, a
directory's own metadata (which files it currently contains) is a separate
piece of durable state from the file contents, and both must be forced to
disk for the write to survive a crash. A `rename` failure because the
destination temporarily vanished (`ENOENT`) is retried once, since another
process cleaning `new/` mid-flight is a recoverable race, not a fatal one.

`writeBatch(_:)` is the bulk twin: it writes and renames every job in the
batch with `writeAndRenameNoFsync`, which skips the per-file durability
step, and forces only `new/`'s directory metadata to disk once at the end.
A crash before that single barrier can lose some just-written jobs, but the
comment records why this is an acceptable trade for a bulk producer
specifically: the reindex that calls `writeBatch` derives its jobs from the
estate itself, so a lost job is simply re-enqueued on the next resume — an
at-least-once guarantee, not a promise that every job survives every
possible crash instant. Ordinary streaming `write` keeps its per-job
durability unchanged; only a caller that opts into the batch API accepts
this weaker bound.

`pendingCount()` counts the files in `new/` — one file, one pending job,
no decoding required, which is why it stays cheap even as a queue grows.

`drainAvailable(stream:)` is the stream-scoped claim. Because the maildir
filename already encodes the stream (`{hlc}-{stream}-{jobid}`), a naive
implementation could filter by filename alone, but the code instead reads
and decodes each file's actual `streamID` field before claiming it, leaving
files whose stream does not match entirely untouched in `new/` — a
concurrent drainer for a different stream is never affected by this one's
scan. `pendingCount(stream:)` performs the equivalent non-claiming count.

`drainAvailable()`, the all-streams claim, renames every file in `new/`
into `cur/` first and only then reads and decodes each one; a file that
fails to decode is moved straight to `done/` rather than left to jam every
future drain pass, since a job whose stored bytes are unparseable can never
become processable by simply trying again.

`reclaimInFlight()` moves everything left in `cur/` back to `new/`. Its doc
comment states its precondition plainly: call it only at mount, before any
drain session is live, because a freshly started process cannot have
claimed anything itself, so anything already sitting in `cur/` must be a
crash orphan from whichever process ran before it.

`watch(handler:)` wires `Watcher.watchNewDirectory` to `drainAvailable()`:
every time the watcher signals that `new/` may have changed, the backend
drains whatever is currently claimable and hands each job to the caller's
handler.

`complete(_:status:artifacts:)` finds the job's file in `cur/` by matching
the filename suffix (every maildir filename ends in `-{jobID}`), writes the
`SignalFile` recording the outcome, and only then moves the job's own file
into `done/`. Writing the signal first matters: if the process crashes
between the two steps, the job is still visible in `cur/` on the next
mount, ready to be reclaimed, rather than silently vanished with an
orphaned signal and no job record.

`completeBatch(_:)` is the batched form: one scan of `cur/` builds a
job-identifier-to-filename index, every completion in the batch is written
without a per-file durability step, and one call forces `done/`'s directory
metadata to disk at the very end. The safety argument mirrors
`writeBatch`'s: a crash before that final barrier leaves the not-yet-moved
jobs in `cur/`, where they are reclaimed and reprocessed — safe because
ingest is idempotent (processing the same job twice causes no harm).

`inFlight()` and `completed(streamID:)` both delegate to the private
`listJobs(in:filter:)`, which lists a directory, skips `.signal` sidecar
files, decodes each remaining file as a `Job`, and optionally filters by
stream.

## PersistenceKitBackend.swift

This file provides `PersistenceKitBackend`, the second concrete backend:
jobs live as rows in a table inside PersistenceKit, MOOTx01's storage kit,
rather than as files on disk. The file's own header comment states five
invariants the implementation is built to preserve; the sections below
explain each one as it is enforced in code.

`QueueKitSchema.declaration()` defines the table's shape: one row per job,
with columns for the job's own fields plus queue bookkeeping — `status`
(`"new"`, `"cur"`, or `"done"`), `signal_status` and `artifacts` (filled in
only once a job completes), and `session_id` (which drain call, if any,
currently holds this row). Three indices back the operations that matter:
one for a plain status count, one on `(status, physical_time,
logical_count, node_id)` for claiming jobs in HLC order, and one on
`(stream_id, status)` for the stream-scoped operations. The table's
`appendOnly` flag is explicitly `false` — invariant 5 — because a queue row
must be mutable: the same row moves from `new` to `cur` to `done` in place.

`write(_:)` is a bare insert into the table with `status: "new"` — no
enclosing transaction, per invariant 1. A transaction would offer no benefit
here (there is nothing else to make atomic with a single insert) and would
only add overhead to the hottest, most frequent operation in the backend.

`writeBatch(_:)` wraps every insert in the batch in one `.readCommitted`
transaction — the transactional-isolation equivalent of the filesystem
backend's single durability barrier. The comment explains why the weaker
`.readCommitted` isolation (rather than the `.serializable` isolation the
claim uses) is still correct here: these are all inserts of brand-new rows,
which cannot conflict with the claim's job of flipping existing `new` rows
to `cur` — a claim that begins before the batch commits simply will not see
the batch's rows yet, and will see them on its next pass instead.

`pendingCount()` and `pendingCount(stream:)` are single-row-count reads
with a `status = 'new'` predicate (and, for the stream form, an additional
`stream_id` predicate) — no claim, no lock beyond an ordinary read.

`drainAvailable(stream:)` and the all-streams `drainAvailable()` both
implement the claim as one `.serializable` transaction containing exactly
two steps: an `UPDATE` that flips every matching `new` row to `cur` under
one freshly minted session identifier, and a `SELECT` that reads back
precisely the rows carrying that session identifier, ordered by HLC. This
single-pass shape — invariant 3, the status-guarded atomic transition — is
what the file's comment calls out as a deliberate improvement over an
earlier design that issued one guarded update per row: claiming a batch of
jobs that way cost one predicate scan per job, so a bulk claim over a large
queue became quadratic in the number of jobs. Reading back by this call's
own unique session, rather than by `status = 'cur'` generally, is what keeps
two concurrent drainers from ever double-counting the same claimed row —
each call's session tags only the rows it itself just claimed.

`completeSession(_:status:)` completes every row claimed under one session
in a single guarded update — the batch twin of the per-job `complete`, and
the reason `QueueKit.reply(session:status:)` has a fast path only on this
backend. It is declared directly on the class rather than as part of the
`QueueBackend` protocol, because only the concrete `PersistenceKitBackend`
handle (not the abstract protocol) has anything meaningful to complete a
whole session against.

`watch(handler:)` subscribes to `storage.observer`, per invariant 2, and
treats every event strictly as a wake signal — it never reads a job's
fields out of the event payload itself, only out of a subsequent
`drainAvailable()` call. `drainUntilEmpty` is the loop this drives: rather
than draining once per event, it drains repeatedly until a pass claims
nothing, because a burst of inserts can coalesce into fewer observer events
than there are actual rows, and draining only once per event would strand
whichever rows' wake got folded away. The initial drain before the
subscription's first `await` matters for the same reason from the other
direction: a job inserted between mount and subscription would otherwise be
invisible to any wake, since its insert event predates the subscription.

`complete(_:status:artifacts:)` is a single guarded update keyed on both
the job identifier and `status = 'cur'`; if nothing matched, the job was
never claimed (or was already completed), so the method throws
`QueueError.jobNotFound` rather than silently doing nothing.

`reclaimInFlight(stream:)` resets a stream's `cur` rows back to `new`,
clearing `session_id`. Its doc comment repeats the same safety argument as
the facade method it backs: this is safe to call only immediately after
successfully acquiring the stream's `DrainLease`, because the lease's
staleness gate is what rules out a live drainer still holding those rows.

`inFlight()` and `completed(streamID:)` both route through the private
`listJobs(status:streamID:)`, and `decodeRow(_:)` turns one database row
back into a `Job`, falling back to an empty extensions dictionary if the
stored JSON for it fails to parse rather than failing the whole read.

## DrainLease.swift

This file provides `DrainLease`, a heartbeat-based, stream-keyed lock that
lets many processes share one estate's queue while guaranteeing that only
one of them actively drains a given stream at a time. Two different streams
get entirely independent leases and can both be held simultaneously — this
is what lets, for example, an encode drainer and a dreaming drainer run at
once without either blocking on the other.

The design is deliberately not a check on whether the prior holder's
process is still alive. A PID-liveness check would need OS-specific code —
querying the process table through platform-specific system calls — which
the file's header comment rules out in favor of something that works
identically on macOS, Linux, and Windows: a lease file holding an owner
string and a wall-clock timestamp. `tryAcquire(now:)` succeeds when the
lease file is absent, already owned by the caller, or its timestamp is
older than `ttl` (fifteen seconds) — in every other case, another drainer
holds a fresh lease, and the caller must stand down. The owner string
itself combines the process identifier with a caller-supplied nonce, so
that if the operating system reuses a process identifier after a crash, the
reused identifier cannot be mistaken for the original holder.

`heartbeat(now:)` refreshes the timestamp while a drainer actively holds
the lease; the doc comment directs a caller to invoke it roughly every five
seconds — well inside the fifteen-second TTL — so an ordinary slow drain
pass never has its own lease expire out from under it. `isHeldByOther(now:)`
answers the same freshness question as `tryAcquire` without attempting to
take the lease, for a caller that only wants to check before deciding
whether to try. `release()` deletes the lease file on clean shutdown, but
only if the caller still owns it, so that a takeover that already happened
is never undone by a late release from the prior holder.

The worst-case cost of a crashed holder is one full TTL: a takeover cannot
happen sooner than fifteen seconds after the last heartbeat, because a
fresher lease has to be honestly waited out. This is the trade the design
accepts for staying free of any OS-specific liveness check.

## Watcher.swift

This file provides `Watcher`, the directory-change wake source
`FilesystemBackend.watch()` is built on. Its job is narrow and its contract
is explicit: call `onChange` whenever the watched directory's contents may
have changed, allowing spurious wakes, because `drainAvailable()` is always
the actual authority on what can be claimed — a false-positive wake costs
one wasted scan, never a correctness problem.

Three platform strategies exist, tried in order of preference.
`watchKQueue` (Darwin only, meaning macOS and iOS) opens the directory with
`O_EVTONLY` and attaches a `DispatchSource` watching kqueue VNODE events —
write, extend, delete, rename — giving wake latency in the microseconds
with no external dependency. `watchLinux` first attempts `openInotify`,
which sets up an inotify watch for `IN_CREATE` and `IN_MOVED_TO` (the
second of which is the event the atomic tmp-to-new rename actually fires);
`runInotifyLoop` then polls that file descriptor with a 500-millisecond
timeout so that task cancellation is still checked promptly between
events, and reads and discards whatever event bytes arrive, since the
handler only needs to know that something happened, not what. If
`openInotify` cannot establish a watch — the kernel's inotify watch limit
is exhausted, or the filesystem does not support inotify — `watchLinux`
falls back automatically to `watchPoll`. Every other platform uses
`watchPoll` directly: a plain 200-millisecond snapshot comparison of the
directory's file listing, correct everywhere but capable of missing a
change for up to one poll interval (100 milliseconds on average).

Every path calls `onChange` once immediately, before entering its
platform-specific wait loop, so that any work already present when the
watcher attaches is not stranded waiting for the next actual filesystem
event. `ContinuationBox` is the small bridge the Darwin path uses to let an
`async` caller wait on a `DispatchSource`'s cancel handler, which fires on
an ordinary `DispatchQueue` callback rather than inside Swift's structured
concurrency.

## QueueKitTelemetry.swift

This file provides `QueueLatencyWindow` and `reportQueueStats`, the
self-report telemetry QueueKit emits through IntellectusLib, MOOTx01's
telemetry library, after every `drain()` call.

The reporting path is additive and deliberately cheap when unused: the
very first check inside `reportQueueStats` is a single atomic flag read,
`Intellectus.isEnabled`, and the function returns immediately when it is
false, at a cost the file's header comment puts at about one nanosecond.
When telemetry is enabled, the function makes one `pendingCount()` call and
emits up to six named metrics, all tagged with the estate identifier and
the kit name so a fleet of estates can be compared metric by metric.

`QueueLatencyWindow` keeps a fixed-capacity rolling list of recent drain
latencies (one hundred samples by default) and computes an arbitrary
percentile from it on demand. `percentile(_:)` guards its input explicitly:
a percentile argument that is not a finite number between 0 and 100 (for
example `NaN` or a value produced by a caller's arithmetic error) returns
`0` rather than computing an array index from it, because converting a
non-finite floating-point value to an integer index can crash the program
outright on some inputs.

`reportQueueStats` treats a failed `pendingCount()` read as its own signal,
not as a zero. Reporting `queue.depth = 0` when the read actually failed
would be indistinguishable from an honestly empty queue, and would tell
whoever is watching the telemetry "everything is drained" when the truth is
"the read itself did not work." Instead, a failed read skips the
`queue.depth` metric entirely, in favor of a dedicated
`queue.depth_unavailable` counter, and every metric that can only be
computed honestly from a known depth (`queue.idle_nonempty`, part of
`queue.head_of_line_age_s`) is skipped along with it. `queue.drain_count`
and the two latency percentiles have no such dependency and are always
reported.

## Rust and Python Ports and Conformance

The `rust/` directory contains a second, independent implementation of the
package: eight source files (`error.rs`, `job.rs`, `backend.rs`,
`filesystem.rs`, `facade.rs`, `drain_lease.rs`, and, behind a
`persistencekit` feature flag, `persistencekit.rs`), reimplementing the
facade, both backends, and the drain lease for non-Apple hosts. The
`python/queuekit/` directory contains a third implementation covering only
`FilesystemBackend` — the package specification restricts Python to that
one backend, since Python tooling has no need of the PersistenceKit
database path.

All three implementations are gated by the same conformance fixtures, kept
in `Tests/QueueKitTests/Fixtures/` as recorded job and completion-signal
input/output pairs. Each fixture pins one exact set of bytes; Rust's own
`rust/tests/conformance.rs` loads the identical fixture files the Swift
test suite reads and asserts that Rust's encoder produces the same bytes
Swift already produced. This is what turns "the wire format is a contract"
from an assertion in this document into something a test suite actually
checks: change a field name, a key order, or an encoding rule in any one
implementation, and the shared fixtures catch the drift immediately in
whichever implementation was not updated to match.
