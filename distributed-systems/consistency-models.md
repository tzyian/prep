# Consistency Models

## Question
What are strong, sequential, causal, and eventual consistency? When do you choose each?

## Short Answer
- Strong/linearizable: operations appear instant and globally ordered; best for correctness‑critical invariants.
- Sequential: per‑client order preserved, but not necessarily real‑time.
- Causal: respects happens‑before; concurrent ops may be seen in different orders.
- Eventual: replicas converge without ordering guarantees; good for high availability.

## Deep Dive
- Linearizability vs serializability: single object vs transactions.
- Causal requires metadata (e.g., vector clocks) and partial order.
- Eventual + CRDTs provide convergence without coordination.

## Follow‑ups
- Relation to [[distributed-systems/cap-theorem]].
- DB isolation mapping: [[databases/transactions-isolation]].

