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
- Contrast with [two-phase-locking](<./two-phase-locking.md>) and [aries-recovery](<./aries-recovery.md>).




# 🐘 MVCC — Multi-Version Concurrency Control

## 🔹 What it is

- MVCC is the concurrency control model PostgreSQL uses.
    
- Instead of locking rows for every read/write, the DB **keeps multiple versions of a row**.
    
- Readers see a **consistent snapshot** of the database at the time their transaction started.
    
- Writers can update rows **without blocking readers** (and vice-versa).
    

---

## 🔹 How it works (Postgres flavor)

- Every row has two hidden system columns:
    
    - `xmin` → transaction ID that **inserted** the row.
        
    - `xmax` → transaction ID that **deleted/updated** the row.
        
- When a row is updated:
    
    - Old row gets its `xmax` set (becomes “dead”).
        
    - A new row version is inserted with a fresh `xmin`.
        
- Readers check these fields against their transaction snapshot to decide which version is **visible**.
    

---

## 🔹 Example Timeline

### Table before:
emp_id | salary | xmin | xmax
-------+--------+------+------
1      | 1000   | 100  | NULL
### T1 (UPDATE salary=2000):

- Old row marked dead (`xmax=101`).
    
- New row version created (`xmin=101`)

emp_id | salary | xmin | xmax
-------+--------+------+------
1      | 1000   | 100  | 101   -- old version
1      | 2000   | 101  | NULL  -- new version

- T2 (a transaction that started before T1 committed) will still see the old row (salary=1000).
    
- T3 (after T1 commits) will see the new row (salary=2000).
    

---

## 🔹 Benefits

- ✅ **Readers never block writers, writers never block readers** (except in conflicting writes).
    
- ✅ Fast `SELECT`s under heavy write load.
    
- ✅ Provides snapshot isolation.
    

---

## 🔹 Downsides

- ❌ Dead rows accumulate → need **VACUUM** (cleanup).
    
- ❌ Old snapshots = extra storage overhead.
    
- ❌ Long-running transactions can prevent cleanup (bloat).
    

---

## 🔹 Isolation in Postgres under MVCC

- **READ COMMITTED** (default): each statement sees committed rows as of its start. If another txn commits between your queries, you’ll see updated data.
    
- **REPEATABLE READ**: all statements in your transaction see the same snapshot (no non-repeatable reads).
    
- **SERIALIZABLE**: adds checks to prevent anomalies like phantoms; emulates serial execution.
- 
Transaction 1          Transaction 2
------------------     -------------------
UPDATE row -> v2
(row v1 xmax set)       SELECT row
                        (still sees v1)
COMMIT
                        SELECT row
                        (now sees v2)
