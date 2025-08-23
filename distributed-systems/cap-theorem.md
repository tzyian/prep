# CAP Theorem

## Question
Explain CAP and how it informs system design under partitions.

## Short Answer
Under a network partition, a system must choose between availability (serve requests) and consistency (single, up‑to‑date view). CP systems sacrifice availability to preserve linearizability; AP systems serve stale or divergent data but converge later.

## Deep Dive
- Partition tolerance is non‑negotiable in distributed systems.
- CP examples: ZooKeeper, etcd; AP examples: Dynamo‑style stores.
- Latency trade‑offs: cross‑region quorum increases tail latency.

## Follow‑ups
- How [[distributed-systems/raft]] behaves during partitions.
- Read repair, hinted handoff (AP tactics).

