# Two-Phase Commit (2PC)

## Question
How does 2PC ensure atomicity across services and what are its failure modes?

## Short Answer
A coordinator asks participants to prepare (vote) and then commit or abort. Atomicity holds if all prepared participants eventually receive the final decision, but 2PC can block if the coordinator fails after prepares (without 3PC or a replicated log).

## Deep Dive
- Phase 1: prepare; write intent/redo to log; lock resources.
- Phase 2: commit/abort; participants acknowledge and release locks.
- Failure: coordinator crash → blocking; participant crash → recovery via logs.
- Mitigations: coordinator replication (e.g., [[distributed-systems/raft]]), timeout + abort in practice.

## Follow-ups
- Contrast with [[databases/transactions-isolation]] and [[databases/mvcc]].

