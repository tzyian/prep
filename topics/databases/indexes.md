# Database Indexes

indexes are faster reads slower writes
query optimisation is NP complete, indexing isn't always fast

cf NoSQL indexes

c.f. AWS Global Secondary Indexes (GSI)

## Question
How do B+‑tree and hash indexes differ and when do you use each?

## Short Answer
- B+‑trees support range and ordered queries (>, <, BETWEEN, ORDER BY) with O(log n) lookups.
- Hash indexes are O(1) average for equality lookups but don’t support ranges or order.

## Deep Dive
- B+‑tree layout keeps keys sorted; leaf nodes are linked for range scans; good locality.
- Write costs: page splits and rebalancing vs hash bucket rehashing.
- Covering indexes can satisfy queries without touching the table.
- Selectivity matters: low‑selectivity predicates benefit less.

## Follow‑ups
- Clustered vs secondary indexes; composite index ordering.

