# Two-Phase Locking (2PL)

## Question
How does 2PL ensure serializability and what are its downsides?

## Short Answer
2PL grows locks while reading/writing and then releases them only after the transaction’s lock acquisition phase ends, yielding conflict‑serializable schedules but risking deadlocks and lower concurrency.

## Deep Dive
- Strict 2PL: hold write locks to commit → simplifies recovery.
- Deadlock handling: wait‑die, wound‑wait, or detection via waits‑for graph.
- Granularity: row/page/table; intention locks enable hierarchy.

## Follow‑ups
- Compare to [[databases/mvcc]] for read/write contention.

