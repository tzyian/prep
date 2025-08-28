# Two-Phase Commit (2PC)

## Question
How does 2PC ensure atomicity across services and what are its failure modes?

## Short Answer
A coordinator asks participants to prepare (vote) and then commit or abort. Atomicity holds if all prepared participants eventually receive the final decision, but 2PC can block if the coordinator fails after prepares (without 3PC or a replicated log).

## Deep Dive
- Phase 1: prepare; write intent/redo to log; lock resources.
- Phase 2: commit/abort; participants acknowledge and release locks.
- Failure: coordinator crash → blocking; participant crash → recovery via logs.
- Mitigations: coordinator replication (e.g., [[raft]]), timeout + abort in practice.

## Follow-ups
- Contrast with [[transactions-isolation]] and [[mvcc]].



# Two-Phase Commit (2PC)

## What is 2PC?
- A **distributed transaction protocol** to ensure **atomicity** across multiple resource managers (e.g., databases, message queues).
- Guarantees: **all participants either commit or abort** a transaction.

---

## Phases of 2PC

### 1. Prepare Phase (Voting Phase)
1. **Coordinator** sends `PREPARE` to all participants.
2. Each **participant**:
   - Writes changes to its **Write-Ahead Log (WAL)**.
   - Ensures it can commit (e.g., locks resources).
   - Replies with `YES` (prepared) or `NO` (abort).

### 2. Commit Phase (Decision Phase)
- If **all participants vote YES**:
  - Coordinator logs **COMMIT** and sends `COMMIT` to all.
- If **any participant votes NO** (or times out):
  - Coordinator logs **ABORT** and sends `ABORT` to all.
- Participants then either finalize changes (commit) or roll back.

---

## Timeline Example

|--- PREPARE ----------->|
|--- PREPARE ------------------------------->|
|<-- YES ----------------|
|<-- YES -----------------------------------|
|--- COMMIT ------------>|
|--- COMMIT -------------------------------->|


---

## Failure Scenarios

1. **Participant fails before voting**
   - Coordinator times out → sends `ABORT`.
   - Safe, no commit happens.

2. **Participant votes YES, then crashes before commit**
   - On recovery, it checks WAL.
   - If decision unknown → it is *in-doubt* until coordinator responds.

3. **Coordinator crashes after receiving all YES but before sending decision**
   - Participants are blocked in "prepared" state, holding locks.
   - They cannot decide safely without coordinator → **blocking problem**.

4. **Network partition between coordinator and participants**
   - Participants that voted YES remain in-doubt.
   - Can’t commit or abort independently → reduced availability.

---

## Properties
- **Atomicity**: All commit or none do.
- **Durability**: WAL ensures recovery after crash.
- **Blocking**: Participants may be stuck if coordinator fails.

---

## Variants & Improvements
- **Presumed Abort/Presumed Commit**: Optimizations to reduce logging overhead.
- **Three-Phase Commit (3PC)**: Adds an extra phase to reduce blocking, but assumes partial synchrony (rare in practice).
- **Consensus-based Commit (Raft/Paxos)**: Used in modern databases to avoid blocking; decision is replicated with fault tolerance.
- **Sagas**: For long-running workflows; instead of atomic global commit, use local commits + compensating actions.

---

## When to Use
- Strong requirement for atomicity across **heterogeneous resources** (e.g., DB + message queue).
- Rare in high-scale cloud DBs due to blocking issues; often replaced with **consensus protocols** or **application-level compensation**.


