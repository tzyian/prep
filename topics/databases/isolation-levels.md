# Question Card: Transaction Isolation Levels

- Question: Compare READ COMMITTED, REPEATABLE READ, and SERIALIZABLE. What anomalies do they allow?
- Role: backend
- Difficulty: mid

## Short Answer
READ COMMITTED prevents dirty reads but allows non‑repeatable reads and phantom reads; REPEATABLE READ prevents dirty and non‑repeatable reads but may allow phantoms (Postgres prevents via predicate locks/SSI); SERIALIZABLE prevents all anomalies by ensuring equivalent to some serial order.


Dirty read: read uncommitted data
Phantom Read: read the same row in a single xact with different values, cos of interleaving commits
Non-repeatable read: collection of rows returned by a query is different cos of interleaving commits


## Deep Dive
- Anomalies: dirty read, non‑repeatable read, phantom, write skew.
- MVCC: snapshots avoid read locks; SERIALIZABLE via SSI detects dangerous structures and aborts.
- 2PL: strict 2PL blocks writers/readers to enforce serializability.

## Example
```sql
-- Non-repeatable read under READ COMMITTED
BEGIN; SELECT balance FROM acct WHERE id=1; -- 100
-- other tx updates to 50 and commits
SELECT balance FROM acct WHERE id=1; -- 50
```

## Follow‑ups
- How indexes and predicate locks affect phantoms.
- Tuning: long‑running transactions and vacuum.

## Pitfalls
- Assuming vendor semantics match SQL standard (they differ!).
- Mixing autocommit reads with transactional writes.

## Checklist
- Anomaly definitions · vendor differences (MySQL vs Postgres) · MVCC vs 2PL basics

## Sources
- [[transactions-isolation]] · [[mvcc]] · [[two-phase-locking]]

