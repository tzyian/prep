# Replicated Logs in Distributed Systems

## What Does "Replicating Logs" Mean?
- Instead of directly replicating the **final state** of a database, nodes replicate a **log of operations**.
- Each node applies the same sequence of log entries to its local state machine → all nodes converge to the same state.

**Example**
- Log entries:  
  1. `SET balance=100`  
  2. `DEPOSIT +20`  
  3. `WITHDRAW -50`  
- All replicas apply entries in the same order → balances remain consistent across the cluster.

This is called **state machine replication**.

---

## Why Replicate Logs Instead of State?
1. **Deterministic replay**: If all replicas replay the same log, they stay consistent.
2. **Auditability**: Logs show the history of changes (good for debugging, auditing).
3. **Recovery**: A crashed node can catch up by replaying missed log entries.
4. **Ordering**: Consensus (Raft/Paxos) ensures the log order is agreed upon → linearizable consistency.
5. **Efficiency**: Replicating incremental log entries is cheaper than sending entire state snapshots repeatedly.

---

## WAL (Write-Ahead Log) vs Replicated Log
- **WAL (local only)**:
  - Each database writes to a WAL before committing to disk.
  - Purpose: crash recovery on the *same node*.
  - Does not replicate across nodes by itself.
  - Example: PostgreSQL WAL (with *logical or physical replication* to other nodes).

- **Replicated Log**:
  - Distributed consensus (Raft/Paxos) ensures multiple nodes maintain the same log.
  - Purpose: cluster-wide consistency & fault tolerance.
  - Example: etcd, CockroachDB, Consul (all use Raft).

---

## CQRS and Event Sourcing
- **CQRS (Command Query Responsibility Segregation)**:
  - Separate the **write model** (append commands/events to a log) from the **read model** (materialized views built asynchronously).
  - Reads may be **eventually consistent**, but scalable and optimized for queries.
  - Relies on log replication (event sourcing).

- **Event Sourcing**:
  - Store **all state changes as events** in a log.
  - System state = replay of the log.
  - Ensures **strong audit trail** and easy recovery.

---

## Ensuring Eventual Consistency
- Logs (replicated via Raft/Paxos) → guarantee **strong consistency** (all nodes see the same order of operations).
- CQRS/Event Sourcing + async propagation → guarantee **eventual consistency** (read models eventually converge to the write log).
- WAL → ensures **durability** of individual nodes, not cluster consistency.

---

## Putting It Together
- **Replicating logs** (Raft, Paxos): keeps a cluster consistent & fault-tolerant.
- **WAL**: protects against *local* crashes.
- **CQRS/Event Sourcing**: scales queries and allows eventual consistency where full strict consistency isn’t needed.
- **Hybrid systems** (e.g., Spanner, CockroachDB):
  - Use a **replicated consensus log** (Raft/Paxos).
  - Each node also has a local **WAL** for durability.
  - Optional **materialized views / read replicas** provide eventual consistency for reads.

