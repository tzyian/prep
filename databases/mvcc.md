# MVCC (Multi-Version Concurrency Control)

## Question
How does MVCC work and what anomalies can still occur?

## Short Answer
MVCC keeps multiple versions of rows with commit timestamps; readers see a snapshot as of their start time, reducing lock contention. It avoids read locks and most anomalies but can allow write skew unless enforced by constraints or SERIALIZABLE.

## Deep Dive
- Versioning: tuple `(value, created_ts, deleted_ts)`; visibility rules by tx start_ts.
- Writers create new versions; garbage collection cleans old ones.
- Serializable via SSI tracks dangerous structures to abort conflicts.

## Follow‑ups
- Contrast with [[databases/two-phase-locking]] and [[databases/aries-recovery]].

