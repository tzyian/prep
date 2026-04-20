# MVCC (Multi-Version Concurrency Control)

## Question
How does MVCC work and what anomalies can still occur?

## Short Answer
MVCC keeps multiple versions of rows with commit timestamps; readers see a snapshot as of their start time, reducing lock contention. It avoids read locks and most anomalies but can allow write skew unless enforced by constraints or SERIALIZABLE.

# MVCC (Multi-Version Concurrency Control)

## Question
How does MVCC work and what anomalies can still occur?

## Short Answer
MVCC keeps multiple versions of rows with commit timestamps; readers see a snapshot as of their start time, reducing lock contention. It avoids read locks and most anomalies but can allow write skew unless enforced by constraints or SERIALIZABLE.

## Deep Dive
- Versioning: tuple `(value, created_ts, deleted_ts)`; visibility rules by transaction start timestamp.
- Writers create new versions; garbage collection (`VACUUM`) reclaims old versions.
- Serializable isolation via SSI detects dangerous structures and aborts conflicting transactions.

## Follow-ups
- Contrast with `two-phase-locking` and `aries-recovery`.

### How it works (Postgres flavor)

- Every row has two hidden system columns:
    - `xmin` → transaction ID that inserted the row.
    - `xmax` → transaction ID that deleted/updated the row.

- When a row is updated:
    - The old row is marked dead (`xmax` set).
    - A new row version is inserted with a fresh `xmin`.

- Readers check these fields against their transaction snapshot to decide which version is visible.

### Example Timeline

Table before:

emp_id | salary | xmin | xmax
-------+--------+------+------
1      | 1000   | 100  | NULL

T1 (UPDATE salary=2000):

- Old row marked dead (`xmax=101`).
- New row version created (`xmin=101`).

emp_id | salary | xmin | xmax
-------+--------+------+------
1      | 1000   | 100  | 101   -- old version
1      | 2000   | 101  | NULL  -- new version

- A transaction that started before T1 committed will still see the old row (salary=1000).
- A transaction that starts after T1 commits will see the new row (salary=2000).

### Benefits

- Readers never block writers, and writers rarely block readers (except on conflicting writes).
- Fast `SELECT` performance under heavy write load.
- Provides snapshot isolation.

### Downsides

- Dead rows accumulate and require `VACUUM` to reclaim space.
- Old snapshots increase storage overhead.
- Long-running transactions can prevent cleanup, causing bloat.

### Isolation in Postgres under MVCC

- **READ COMMITTED** (default): each statement sees the latest committed rows at the time the statement starts.
- **REPEATABLE READ**: all statements in a transaction see the same snapshot.
- **SERIALIZABLE**: adds additional checks to prevent anomalies like phantoms and emulates serial execution.

Example interaction:

Transaction 1          Transaction 2
------------------     -------------------
UPDATE row -> v2
(row v1 xmax set)       SELECT row
                                                (still sees v1)
COMMIT
                                                SELECT row
                                                (now sees v2)
- Provides snapshot isolation.
