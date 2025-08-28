# Eventual Consistency

## Definition
- A **consistency model** used in distributed systems (especially AP systems).
- Guarantees that **if no new updates are made**, all replicas in the system will **eventually converge** to the same state.
- Reads may be **stale** (not reflect the latest write), but the system "catches up" over time.

---

## Key Properties
- **High Availability**: Nodes can always accept reads/writes, even during partitions.
- **Temporary Inconsistency**: Different nodes may show different data versions.
- **Convergence**: Given enough time (and no new writes), all replicas synchronize.

---

## Examples
- **DNS**: Updates (like a new IP for a domain) take time to propagate globally.
- **Shopping Cart (Amazon Dynamo)**: You may add an item on one device, and see a delay before it appears on another device.
- **Cassandra / DynamoDB**: Writes are accepted everywhere; background processes reconcile replicas.

---

## Implications
- Users might see **different results depending on which replica** they query.
- Developers often need **conflict resolution** strategies:
  - *Last-write-wins (LWW)*  
  - *Vector clocks*  
  - *Merge logic (e.g., union of shopping cart items)*

---

## Contrast with Strong Consistency
- **Strong Consistency**: Every read reflects the latest committed write (linearizability).
- **Eventual Consistency**: Reads may reflect an older state, but will converge eventually.
