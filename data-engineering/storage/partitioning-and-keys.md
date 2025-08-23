# Question Card: Partitioning and Keys

- Question: How do you choose partition keys and manage skew in data systems (Kafka/Spark/DBs)?
- Role: data-eng
- Difficulty: mid

## Short Answer
Choose keys that spread load evenly while preserving locality for common queries or joins. Detect and mitigate skew via salting, composite keys, bucketing, or adaptive partitioning. Monitor hottest keys and rebalance.

## Deep Dive
- Kafka: partition by key for order per key; beware hot keys; increase partitions and use sticky partitioner.
- Spark: partitioner for joins/aggregations; skew hints, salting, AQE skew join.
- DBs: hash vs range partitioning; secondary indexes and hotspots.

## Example
```text
Salting: key:= userId||hash(userId)%N to spread hotspots
```

## Follow‑ups
- Repartitioning online: dual‑write/migration strategies.
- Impact on exactly‑once and ordering guarantees.

## Pitfalls
- Single hot key dominating a partition; rehashing without backfill plan.

## Checklist
- Load distribution · locality · skew detection · migration strategy

## Sources
- Kafka, Spark AQE docs

