# CAP

- Consistency → Every read sees the most recent write (single, up-to-date view of data).
- Availability → Every request receives a (non-error) response, even if it may not be the latest.
- Partition Tolerance → System continues to function despite arbitrary message loss or network splits.

# Trade-off: 
In presence of a partition, a distributed system must choose between Consistency and Availability.

- CA (no Partition Tolerance) → Possible only in single-node or strongly connected systems (traditional RDBMS).
- CP → Prioritizes Consistency over Availability (e.g., HBase, MongoDB with strong consistency settings).
- AP → Prioritizes Availability over Consistency (e.g., DynamoDB, Cassandra, CouchDB).

# CAP Theorem — Trade-offs and Implications

## 1. When a Partition Occurs
- A **partition** = network split / node isolation → some nodes cannot communicate.
- Under partitioning:
  - If nodes continue accepting requests → risk **inconsistency**.
  - If nodes refuse requests until healed → risk **unavailability**.
- Therefore: **must sacrifice either C or A** (while always keeping P).

---
## 2. Trade-off Scenarios

### CP (Consistency + Partition Tolerance, sacrifice Availability)
- **Examples**: HBase, MongoDB (with majority writes), Zookeeper
- Behavior: System refuses writes (and sometimes reads) without quorum.
- ✅ Strong correctness — all clients see the same data.
- ❌ Users may face timeouts / errors during partitions.
- **Implication**: Suited for banking, ledgers, metadata systems.

---
### AP (Availability + Partition Tolerance, sacrifice Consistency)
- **Examples**: Cassandra, DynamoDB, CouchDB
- Behavior: Nodes accept requests even when isolated.
- ✅ High availability and responsiveness.
- ❌ Conflicts may occur → rely on eventual consistency / conflict resolution.
- **Implication**: Good for shopping carts, social feeds, caching.
---
### CA (Consistency + Availability, sacrifice Partition Tolerance)
- **Examples**: Traditional RDBMS (Postgres, MySQL single instance)
- Behavior: Every request gets the latest data, no downtime — but only if no partition.
- ✅ Consistent and available in single-node or tightly-coupled clusters.
- ❌ Does not scale well across unreliable networks.
- **Implication**: Works for non-distributed systems.

---
## 3. Broader Implications

- **System design choices**:
  - Banking → **CP** (reject > risk double-spend).
  - Social Media → **AP** (show stale feed > error out).
  - Caching → **AP** (fast > consistent).
  
- **Latency vs UX**:
  - CP → may appear slow/unavailable during partitions.
  - AP → always online but potentially inconsistent.

- **Consistency spectrum** (not just binary):
  - Strong consistency → reads reflect latest writes.
  - Eventual consistency → system converges over time.
  - Intermediate: causal, monotonic, read-your-writes guarantees.

---

## 4. CAP Triangle (Examples)

```
       Partition Tolerance (P)
             /\
            /  \
           /    \
          /      \
         /        \
        /          \
       /            \
Consistency (C) ---- Availability (A)

```