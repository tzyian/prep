# Distributed Transactions Beyond 2PC

## Why Not 2PC?
- **Blocking problem**: If coordinator dies after `PREPARE`, participants are stuck "in-doubt".
- **Single point of failure**: Coordinator availability determines progress.
- **Poor fit for large-scale cloud DBs**: High latency + locks across many nodes.

So modern systems use **consensus protocols (Raft/Paxos)** instead of a fragile central coordinator.

---

## Alternatives Used in Practice

### 1. Consensus-based Commit (Raft/Paxos)
- Each **transaction commit record** is written into a **replicated consensus log**.
- Once a majority (quorum) agrees, the transaction is considered committed.
- Removes blocking: if the leader crashes, a new leader is elected and continues.
- **Examples**:
  - **Spanner** (Google): uses **Paxos groups** for replication + 2PC *between groups* (but with Paxos to avoid blocking).
  - **CockroachDB / TiDB**: use **Raft groups** for data ranges, transaction metadata also replicated with Raft.

---

### 2. Sagas (Long-running Transactions)
- Break a large transaction into a **sequence of local transactions**.
- Each step commits immediately. If later steps fail → run **compensating transactions** to undo earlier steps.
- Trades **strict atomicity** for **availability and resilience**.
- Common in **microservices** (e.g., order → payment → shipping).
- **Example**: Airline booking — reserve seat, charge payment, confirm booking. If payment fails, seat reservation is cancelled.

---

### 3. Per-Partition Consensus + Transaction Layer
- Data is **sharded into ranges/partitions**, each managed by a Raft/Paxos group.
- A transaction spans multiple ranges:
  - Coordinator is lightweight (stores metadata in a consensus log).
  - Reads/writes go to relevant partitions; final commit is written via consensus.
- Guarantees **serializability** without global blocking.
- **Examples**: Spanner, CockroachDB, YugabyteDB.

---

### 4. MVCC (Multi-Version Concurrency Control) + Consensus
- Transactions read/write **versions** of data.
- Commit timestamp assigned via **TrueTime (Spanner)** or hybrid logical clocks (CockroachDB).
- Consensus ensures log order; MVCC ensures readers don’t block writers.
- Enables **serializable isolation** with high concurrency.

---

### 5. Application-level Solutions
- **Idempotent operations**: Safe retries instead of strict atomic commits.
- **At-least-once messaging + deduplication**: Often simpler than full distributed transactions.
- **Event sourcing / CQRS**: Record events in an append-only log (Kafka, Pulsar) instead of global atomic commits.

---

## Summary
- **2PC** is theoretically simple but practically brittle.
- Modern systems achieve distributed transactions using:
  - **Consensus replication (Raft/Paxos)** for fault-tolerant commit logs.
  - **MVCC + hybrid clocks** for serializable isolation.
  - **Sagas / compensation** for cross-service workflows.
- Real-world examples:
  - **Spanner**: Paxos + TrueTime + 2PC (non-blocking, because Paxos backs decisions).
  - **CockroachDB**: Raft + HLC (Hybrid Logical Clocks) + distributed txn protocol.
  - **YugabyteDB**: Raft + DocDB transactional layer.
  - **Microservices**: Often Sagas or eventual consistency instead of strict distributed transactions.

