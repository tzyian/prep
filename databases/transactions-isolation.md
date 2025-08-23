# Transactions and Isolation Levels

## Question
Explain ACID and common SQL isolation levels with typical anomalies.

## Short Answer
ACID: atomicity, consistency, isolation, durability. Isolation levels (ANSI): Read Uncommitted, Read Committed, Repeatable Read, Serializable; they trade performance for anomaly prevention (dirty/nonrepeatable/phantom reads).

## Deep Dive
- Read Committed: prevents dirty reads; allows nonrepeatable/phantom.
- Repeatable Read: prevents dirty and nonrepeatable; phantoms may remain unless predicate locks or MVCC gap locks.
- Serializable: full correctness via 2PL, SSI, or predicate locking.
- Snapshot isolation (popular): no read locks; prevents many anomalies but allows write skew.

## Follow‑ups
- Map to [[databases/mvcc]] and 2PL; compare [[distributed-systems/consistency-models]].

