# 2PC vs Raft

## What they solve
- **2PC (Two-Phase Commit)**:
  - Protocol for *atomic commit* across **multiple resource managers** (DBs, message queues, services).
  - Goal: all participants either commit or abort a transaction.
  - Assumes a **single coordinator**.

- **Raft**:
  - Consensus algorithm for **replicated logs** within a **single distributed system** (like a cluster of DB replicas).
  - Goal: all nodes agree on the same sequence of operations, even under failures.
  - Uses **leader election + majority quorums**.

---

## Core Differences

| Aspect                | 2PC                                     | Raft                                   |
|-----------------------|-----------------------------------------|----------------------------------------|
| **Problem Domain**    | Distributed transaction commit          | Consensus + replicated state machine    |
| **Coordination**      | Single coordinator decides              | Leader elected by majority              |
| **Failure Tolerance** | Coordinator crash → **blocking problem** | Leader crash → new leader elected safely |
| **Quorum**            | Not required (just YES/NO votes)        | Majority quorum required                |
| **Durability**        | WAL at each participant                 | Replicated log across majority          |
| **Progress**          | Can stall indefinitely (blocking)       | Progress continues if majority alive    |

---

## Why 2PC is Rare in Practice
- **Blocking**: If coordinator dies after participants vote YES, they are stuck *in-doubt* (holding locks, resources) until recovery.
- **Availability hit**: A single coordinator is a single point of failure.
- **Heavyweight**: Requires all participants to log and coordinate.

**Used in limited cases**:
- Cross-resource transactions in monolithic enterprise systems (XA transactions, legacy middleware).
- Not favored in large-scale cloud/distributed DBs.

---

## Why Raft (or Paxos) is Preferred
- Modern distributed databases (e.g., Spanner, etcd, CockroachDB, TiDB, Consul) use **Raft or Paxos** to replicate logs and ensure consensus.
- Raft avoids blocking: if leader crashes, a new one is elected.
- Provides both **consistency** and **availability** under majority quorum, whereas 2PC sacrifices availability.

---

## Important Distinction
- **Raft ≠ a replacement for 2PC** directly:
  - Raft is about **replicating logs inside one system**.
  - 2PC is about **coordinating multiple independent systems**.
- But in practice, many systems avoid 2PC by:
  - Using Raft for internal consensus instead.
  - Redesigning apps to use **idempotent operations**, **Sagas (compensating transactions)**, or **CQRS/event sourcing**.

---

## Summary
- **2PC**: Atomic commits across heterogeneous systems; rarely used at scale due to blocking.
- **Raft**: Fault-tolerant consensus for replicated state machines; heavily used in practice.
- Modern systems prefer **Raft/Paxos for internal consistency** and **application-level compensation** (instead of 2PC) for cross-service workflows.
