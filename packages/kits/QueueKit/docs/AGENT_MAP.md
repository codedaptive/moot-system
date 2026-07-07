---
doc: AGENT_MAP
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

# AGENT_MAP: QueueKit

PURPOSE: general-purpose durable work queue. Producer→`send`/`writeBatch`→backend; consumer→`drain`/`watch`→claims jobs; consumer→`reply`→terminal completion. Two swappable backends (`FilesystemBackend` POSIX maildir, `PersistenceKitBackend` shared SQLite table) behind one `QueueBackend` protocol and one public `QueueKit` facade. Stream-scoped ops (ADR-021 Decision 7 / T1) let multiple consumers share one queue without stealing each other's jobs.

DEPS: imports SubstrateTypes (HLC/HLCGenerator: hybrid logical clock), PersistenceKit (Storage, rowStore, transaction, observer: PersistenceKitBackend only), IntellectusLib (self-report telemetry, DECISION_LIFT_PACKAGE_SWIFT_RULE_2026-05-28). ConvergenceKit is application-layer composition and deliberately NOT a dependency (spec §11). Imported by: none within this repo checkout; source comments name CorpusKit and GeniusLocusKit as external mount-time consumers (not present in moot-system). Rust port in rust/ (8 files, ~3k lines) mirrors facade + both backends, PersistenceKitBackend behind `persistencekit` cargo feature. Python port in python/queuekit/ mirrors FilesystemBackend only (spec §2: Python has no PersistenceKit backend). All three gated by shared conformance fixtures in Tests/QueueKitTests/Fixtures/*.json.


CURRENT TRUE-UP:
- v1.0.24: queue metrics sample every drain tick but emit at most once per 30 seconds per estate stream. `QueueLatencyWindowBox` owns both window state and throttle state under one lock.

ENTRY POINTS (most callers need only these):
- QueueKit.swift:53 `QueueKit.init(root:hlcGenerator:) throws`: mount FilesystemBackend at root, create maildir, clean stale tmp/
- QueueKit.swift:66 `QueueKit.init(backend:root:)`: mount explicit backend (PersistenceKitBackend, or a test double)
- QueueKit.swift:73 `send(_ job: Job) async throws`: enqueue one job
- QueueKit.swift:86 `drain() async throws -> [(job: Job, sessionID: SessionID)]`: claim all available jobs
- QueueKit.swift:130 `reply(to:status:artifacts:) async throws`: mark one claimed job terminal
- QueueKit.swift:124 `watch(handler:) async throws`: subscribe a per-job handler

## Symbol Table

### Facade: QueueKit.swift
- :28 `staleTmpThreshold = 5 * 60`: tmp/ files older than this on init are deleted (crash between write and rename; never visible in new/, safe to remove)
- :30 `final class QueueKit: Sendable`
- :40 `latencyWindow: QueueLatencyWindowBox`: lock-backed, thread-safe; concurrent drain() calls (encode worker + import worker sharing an estate queue) both report through the same lock, never a raw var
- :48 `estateTag: String = "unknown"`: nonisolated(unsafe); set ONCE at mount before any drain(), never again
- :53 `init(root:hlcGenerator:) throws`: see ENTRY POINTS
- :66 `init(backend:root:)`: see ENTRY POINTS
- :73 `send(_:)` / :82 `send(batch:) -> Int`: writeBatch twin; FS backend fsyncs new/ ONCE for the batch
- :86 `drain()` / :109 `drain(stream:)`: claim; wraps backend call with reportQueueStats timing
- :124 `watch(handler:)`: forwards to backend.watch
- :130 `reply(to:status:artifacts:)`: guards status.isTerminal, throws .invalidTerminalStatus otherwise
- :149 `reply(session:status:) -> Int`: fast path ONLY on PersistenceKitBackend (`as?` cast); returns 0 on FilesystemBackend (caller falls back to per-job reply)
- :172 `reply(batch:) -> Int`: routes to backend.completeBatch; FS backend's one-scan/one-fsync path
- :194 `reclaimInFlight(stream:) -> Int`: GATE: call ONLY immediately after DrainLease.tryAcquire SUCCEEDED for stream; fast path ONLY on PersistenceKitBackend, returns 0 otherwise
- :205 `inFlight()` / :215 `pendingCount()` / :225 `pendingCount(stream:)`: read-only depth probes; pendingCount()+inFlight().count = total outstanding work
- :275 `awaitDrain(pollInterval: .milliseconds(20), timeout: .seconds(30)) throws`: polls both frontiers; returns promptly if already empty; PROGRESS-BASED deadline (resets whenever outstanding drops below its lowest seen value, not a total wall-clock cap); throws .drainTimeout only on a true stall, never hangs
- :319 `awaitDrain(stream:pollInterval:timeout:)`: stream-scoped twin; same progress-based deadline; global awaitDrain would block forever on OTHER streams' jobs a scoped drainer never claims
- :349 `completed(streamID:)`
- :357 `maildirSubdirs = ["tmp","new","cur","done"]`
- :359 `ensureMaildir(root:) throws`: creates 4 subdirs if absent
- :376 `cleanStaleTmpFiles(root:) throws`: deletes tmp/ entries older than staleTmpThreshold

### Backend contract: QueueBackend.swift
- :9 `protocol QueueBackend: Sendable`
- :10 `write(_:)`, :23 `drainAvailable()`, :30 `watch(handler:)`, :34 `complete(_:status:artifacts:)`, :52 `inFlight()`, :54 `completed(streamID:)`: must-implement core
- :21 `writeBatch(_:) -> Int`: default (extension :92-102) loops write; override for shared-cost batching
- :28 `pendingCount()`: depth probe used by telemetry
- :48 `completeBatch(_:) -> Int`: default (extension :104-116) loops complete; override for shared-cost batching
- :66 `drainAvailable(stream:)`: default (extension :76-84) claims ALL streams then filters: steals other streams' jobs, MUST override for real stream isolation
- :73 `pendingCount(stream:)`: default (extension :86-89) delegates to all-streams pendingCount() (wrong count unless overridden)

### Wire types: Job.swift
- :22 `struct JobID: RawRepresentable`: :31 `generate()` = UUID → 32 lowercase hex, no hyphens (avoids colliding with maildir filename hyphen separator)
- :51 `struct StreamID: RawRepresentable`: workload/lane name
- :68 `struct SessionID: RawRepresentable`: :75 `mint()` = one per drain() call; groups a claimed batch
- :80 `struct ToolName: RawRepresentable`
- :85 `enum ArtifactRef`: file_path|commit_hash|signal_file|trajectory_step_id; explicit `type` field on wire (:91 CodingKeys)
- :131 `indirect enum CodableValue`: open caller extensions blob: null/bool/int/double/string/array/object; round-trips verbatim
- :167 `struct Job: Identifiable, Hashable`: id/streamID/submittedAt(HLC)/priority/payload/extensions
- :193 `Job` CodingKeys: PINNED snake_case wire keys: id, stream_id, submitted_at{physical_time,logical_count,node_id}, priority, payload(base64url), extensions
- :254 `struct MissionContext`: ONLY type using default camelCase Codable synthesis (not part of pinned job envelope)
- :236 `WireFormat.base64urlEncode(_:)` / :245 `base64urlDecode(_:)`: RFC 4648 §5, no padding
- :289 `WireFormat.filename(for:) -> String`: `{sortableHLC}-{streamID}-{jobID}`
- :296 `WireFormat.sortableHLC(_:)`: `{phys:016d}-{logical:08d}-{node_unsigned:010d}`; zero-padding makes lexicographic filename sort == HLC sort
- :307 `WireFormat.encoder`: sortedKeys + withoutEscapingSlashes: byte-stable across Swift/Rust/Python (conformance requirement)
- :313 `WireFormat.decoder`
- :321 `struct SignalFile`: job_id/status/artifacts/completed_at; same pinned-key discipline as Job

### Status/errors
- ObservationStatus.swift:9 `enum ObservationStatus: String`: running|done|done_with_concerns|needs_context|blocked
- ObservationStatus.swift:16 `isTerminal`: only .running is false; gates every reply/complete call
- QueueError.swift:7 `enum QueueError: Error`: directoryCreationFailed, writeFailed, renameFailed, decodingFailed, unknownTool, jobNotFound, watcherFailed, staleTmpFile, backendUnavailable, invalidTerminalStatus, drainTimeout(pending:inFlight:), invalidIdentifier(id:reason:)

### FilesystemBackend.swift (POSIX maildir; semantics derived from Postfix deliver_maildir())
- :43 `final class FilesystemBackend: QueueBackend, @unchecked Sendable`
- :32 `private final class HLCBox`: NSLock-guarded HLCGenerator (value type; concurrent send() must serialize)
- :74 `validateIdentifier(_:) throws` (private static): rejects empty/`.`/`..`/path-separator/control-char; MUST run before ANY path is built from a caller identifier (path-traversal guard, CAND-023, mirrored in Rust+Python)
- :96 `write(_:)`: validate → O_CREAT|O_EXCL write to tmp/, mode 0600, fsync, rename to new/, fsync new/ dir
- :129 `writeBatch(_:) -> Int`: per-job write+rename WITHOUT fsync, ONE fsync of new/ dir at end; AT-LEAST-ONCE (reindex re-derives from estate on crash)
- :160 `atomicWriteAndRename(data:tmpPath:newPath:newDir:) throws` (static): the full durable write; ENOENT rename retried once, EXDEV (tmp/new on different filesystems) fails hard
- :273 `writeAndRenameNoFsync(data:tmpPath:newPath:) throws` (static): batch primitive, no per-file/dir fsync
- :340 `pendingCount()`: file count in new/
- :363 `drainAvailable(stream:)`: reads+decodes each new/ file BEFORE claiming (read ≠ claim); only rename-claims if job.streamID == stream; non-matching files left untouched in new/ (no steal-then-unclaim)
- :428 `pendingCount(stream:)`: decodes each new/ file, non-claiming
- :445 `drainAvailable()`: rename ALL new/→cur/ first, then decode; undecodable file → moved to done/ (poison, never retried)
- :516 `reclaimInFlight() -> Int`: GATE: call ONLY at mount before any drain session is live (fresh process owns no in-flight work by construction); unscoped (cur/ has no per-stream separation)
- :549 `watch(handler:)`: Watcher.watchNewDirectory → drainAvailable() → handler per job
- :567 `complete(_:status:artifacts:)`: find file in cur/ by `-{jobID}` suffix; writes SignalFile to done/ BEFORE renaming job file (crash between = job still reclaimable, never orphaned-signal-no-job)
- :631 `completeBatch(_:) -> Int`: ONE cur/ scan → jobID→filename index (keys on suffix after LAST `-`; JobIDs are 32 dashless hex so this is unambiguous); per-completion write+rename without fsync; ONE done/ dir fsync at end
- :701 `listJobs(in:filter:)` (private): skips `.signal` sidecars, decodes, optional stream filter

### PersistenceKitBackend.swift (spec §10 v1.1: 5 invariants enforced)
- :34 `queueKitTableName = "queuekit_jobs"`
- :36 `enum QueueKitSchema`: :40 `declaration()`: columns incl. status/session_id/signal_status/artifacts; :62 `appendOnly: false` MUST stay false (invariant 5); indices: status, (status,phys,logical,node) claim-order, (stream_id,status)
- :84 `final class PersistenceKitBackend: QueueBackend, @unchecked Sendable`
- :91 `openSchema(on:) throws` (static): opens schema on a Storage
- :97 `write(_:)`: BARE rowStore.insert, status="new", NO transaction (invariant 1)
- :143 `writeBatch(_:) -> Int`: ONE `.readCommitted` transaction wrapping N inserts; readCommitted safe because new-row inserts cannot conflict with the claim's serializable new→cur UPDATE (claim sees pre-batch snapshot or post-batch rows, never a torn view)
- :172 `pendingCount()`: COUNT WHERE status='new'
- :189 `drainAvailable(stream:)` / :248 `drainAvailable()`: SINGLE-PASS CLAIM (invariant 3): one `.serializable` txn = guarded bulk UPDATE new→cur under fresh session_id, THEN SELECT rows WHERE session_id=session (same txn, sees own writes); O(N) not O(N²); session-keyed readback prevents cross-drainer double-count
- :237 `pendingCount(stream:)`: COUNT WHERE status='new' AND stream_id=?
- :324 `completeSession(_:status:) -> Int`: NOT on QueueBackend protocol (concrete-only); ONE guarded UPDATE WHERE session_id=? AND status='cur'; backs QueueKit.reply(session:status:)
- :350 `watch(handler:)`: storage.observer.observe(insert) per invariant 2 (event = wake ONLY, never read job fields from event); drains once BEFORE first await (catches pre-subscription inserts), then per wake
- :378 `drainUntilEmpty` (private static): loops drainAvailable() until a pass claims 0; absorbs coalesced/dropped observer wakes; a claim ERROR must propagate (not `try? ?? []`) or a fault masquerades as "queue empty"
- :400 `complete(_:status:artifacts:)`: guarded UPDATE WHERE id=? AND status='cur'; affected==0 → throws .jobNotFound
- :451 `reclaimInFlight(stream:) -> Int`: GATE identical to FilesystemBackend.reclaimInFlight: ONLY after DrainLease.tryAcquire success for stream; resets cur→new, clears session_id, stream-scoped (shared table holds multiple streams)
- :468 `inFlight()` / :472 `completed(streamID:)` → :480 `listJobs(status:streamID:)` (private)
- :500 `decodeRow(_:)` (private static): malformed extensions JSON → falls back to empty dict, not a decode failure

### DrainLease.swift: stream-keyed heartbeat-TTL lock (NOT PID-liveness; portable, no FFI)
- :49 `struct DrainLease: Sendable`: pure value type; lets Corpus's non-isolated deinit call release() directly
- :55 `owner`: `"pid-{pid}-{instanceToken}"`; nonce defeats PID-reuse impersonation after crash
- :59 `ttl`: default 15s = Rust DRAIN_LEASE_TTL_SECS
- :64 `heartbeatInterval = 5` (static): heartbeat cadence, well inside ttl
- :73 `init(directory:stream:instanceToken:ttl:)`: lease file = `<dir>/<safeStream>.drain.lease`; stream name sanitized to alnum/_/- (else `_`)
- :93 `tryAcquire(now:) -> Bool`: false only if fresh lease held by ANOTHER owner; else (re)claims + re-reads to resolve write races (atomic replace = last-writer-wins)
- :110 `heartbeat(now:)`: refresh; does NOT check ownership, caller's responsibility
- :119 `isHeldByOther(now:) -> Bool`: freshness check without attempting acquire
- :127 `release()`: deletes file ONLY if still owned by self (never undoes a takeover)
- File format: `<owner>\n<epochSeconds>`, atomic replace write

### Watcher.swift: directory wake source (spurious wakes OK; drainAvailable is authority)
- :27 `enum Watcher`
- :37 `watchNewDirectory(at:onChange:) throws`: calls onChange() once immediately (drain pre-attach work) before platform loop
- :63 `watchKQueue` (Darwin only): DispatchSource kqueue VNODE (write/extend/delete/rename), O_EVTONLY fd, µs latency
- :134 `openInotify` (Linux, private): IN_CREATE|IN_MOVED_TO; returns -1 on watch-limit/unsupported-fs (caller falls back)
- :162 `runInotifyLoop` (Linux, private): poll() 500ms timeout for responsive cancellation; drains fd, doesn't parse events
- :216 `watchPoll`: 200ms snapshot-diff poll; Linux fallback AND sole watcher on other platforms; ~100ms average latency
- :240 `final class ContinuationBox`: bridges DispatchSource cancel handler (DispatchQueue callback) to async wait()

### Telemetry: QueueKitTelemetry.swift (off-path cost ~1ns; metric namespace `queue.*`)
- :31 `struct QueueLatencyWindow`: capacity-100 rolling sample list; NOT itself synchronized, concurrent access goes through the box below
- :48 `percentile(_:) -> Double`: P7-secfix: guards non-finite/out-of-range p BEFORE index computation (NaN/inf could crash)
- :70 `final class QueueLatencyWindowBox: Sendable`: Mutex-backed thread-safe holder; encode worker and import worker share one window across concurrent drain() calls
- :79 `sample(_:) -> (p50: Double, p95: Double)`: appends the sample and reads both percentiles under ONE lock acquisition, so concurrent drains cannot interleave append and read
- :103 `reportQueueStats(backend:drained:drainStart:now:estateTag:window: QueueLatencyWindowBox) async`: gate: `Intellectus.isEnabled`; emits queue.depth (or queue.depth_unavailable on read failure: NEVER fabricates 0), queue.drain_count, queue.idle_nonempty (skipped if depth unknown), queue.latency_p50_ms, queue.latency_p95_ms, queue.head_of_line_age_s; window param is now the box, not an inout raw window

## INVARIANTS / GOTCHAS

- WIRE FORMAT IS THE CONTRACT. Job/SignalFile Codable keys (snake_case, nested HLC, base64url payload) are pinned across Swift/Rust/Python. Any change must be mirrored in rust/src/job.rs and python/queuekit/job.py and pass the shared fixtures in Tests/QueueKitTests/Fixtures/*.json (rust/tests/conformance.rs reads the SAME files).
- JobID.generate() deliberately omits hyphens (32 dashless hex): required so WireFormat.filename's own hyphen separators are unambiguous. Do not "prettify" back to UUID-with-hyphens form.
- validateIdentifier (FilesystemBackend) MUST run before any path is constructed from a caller-supplied stream_id or job id, at EVERY write/complete/completeBatch entry point. Mirrored in Rust and Python: edit all three or none.
- reclaimInFlight (both backends) is safe ONLY immediately after a successful DrainLease.tryAcquire for that stream. Calling it without a fresh lease can yank a job out from under a live drainer. FilesystemBackend's unscoped reclaimInFlight() is mount-time-only, by construction (fresh process = no in-flight work of its own).
- reply(session:status:) and reclaimInFlight(stream:) have real fast paths ONLY on PersistenceKitBackend (checked via `as?`); every other backend returns 0, and callers MUST have a per-job fallback.
- QueueBackend's default drainAvailable(stream:) and pendingCount(stream:) extensions are CORRECTNESS TRAPS if relied upon: the default drain claims and then re-releases other streams' jobs (transient full-queue claim), and the default pendingCount(stream:) reports the ALL-STREAMS count. Both concrete backends override correctly; a THIRD backend implementation MUST also override both.
- PersistenceKitBackend.write() is a bare insert, NEVER wrap it in a transaction (invariant 1). writeBatch's .readCommitted (not .serializable) is deliberate: see drainAvailable's serializable claim for why this is still race-free.
- PersistenceKitBackend.watch()'s observer event is a WAKE SIGNAL ONLY (invariant 2): never read job data from the TableChange event; always re-enter through drainAvailable(). drainUntilEmpty must propagate claim errors, never `try? ?? []`, or a live fault silently reads as "queue empty."
- appendOnly MUST stay false on the queuekit_jobs table declaration (invariant 5): rows are mutated in place through the new→cur→done lifecycle.
- FilesystemBackend.completeBatch's jobID lookup keys on the filename suffix after the LAST `-`. This is only unambiguous because JobIDs are 32 dashless hex characters (JobID.generate()); a job id containing a `-` would break this index.
- estateTag on QueueKit is `nonisolated(unsafe)`: set it ONCE at mount, before any drain() call, never again during concurrent use. The internal latencyWindow does NOT follow this discipline: it is a `QueueLatencyWindowBox`, lock-backed, and is written on every concurrent drain() call by design (encode worker + import worker sharing an estate queue).
- awaitDrain / awaitDrain(stream:) are polling latches (20ms default interval, 30s default timeout), not push notifications: there is no native "queue just emptied" event on either backend. Always returns promptly on an already-empty queue. The timeout is PROGRESS-BASED, not a total wall-clock cap: it resets every time outstanding (pending+inFlight) drops below its lowest observed value, so a slow-but-progressing drain never false-times-out. Only a true stall (no frontier movement for the full timeout) throws QueueError.drainTimeout.
- Telemetry never fabricates queue.depth=0 on a read failure: it emits queue.depth_unavailable instead, and skips every metric that depends on a known depth. Do not "simplify" this to `(try? pendingCount()) ?? 0`.
- Watcher wakes are ALWAYS advisory/spurious-tolerant. drainAvailable() (via atomic rename / serializable UPDATE) is the sole claim authority on every platform.
- DrainLease is heartbeat-TTL, not PID-liveness: worst-case takeover latency is one full TTL (15s). Do not add OS-specific process-liveness checks; portability across macOS/Linux/Windows is a design constraint, not an oversight.
- Pinned constants: changing requires a conformance regen across all 3 ports: staleTmpThreshold 300s, DrainLease ttl 15s / heartbeatInterval 5s, Watcher poll interval 200ms / Linux poll() timeout 500ms, QueueLatencyWindow capacity 100, awaitDrain pollInterval 20ms / timeout 30s, NovelToken-style file mode 0600 on queue files.
- ConvergenceKit is intentionally NOT a QueueKit dependency (spec §11): it is application-layer composition sitting above this kit. Do not add it as a Package.swift dependency.
