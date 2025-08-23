# Question Card: 2PC vs Sagas

- Question: Compare two-phase commit (2PC) and Sagas for distributed transactions. When do you choose each?
- Role: backend
- Difficulty: advanced

## Short Answer
2PC coordinates atomic commit across participants using a blocking coordinator; it provides all‑or‑nothing but can block on coordinator failure. Sagas break a transaction into a sequence of local transactions with compensations; they avoid blocking and scale better but provide only eventual consistency.

## Deep Dive
- 2PC: prepare then commit; coordinator logs decisions (WAL); participants hold locks until commit/abort.
- Sagas: choreographed (events) or orchestrated (central controller); define compensating actions for failure.
- Consistency: 2PC aims for atomicity; Sagas provide business‑level invariants with compensations.

## Example
```text
Order saga: reserve inventory -> charge payment -> create shipment
Compensate on failure: release inventory, refund payment, cancel order
```

## Follow‑ups
- Idempotency and dedup for events/steps.
- Outbox pattern to publish reliably.

## Pitfalls
- 2PC over unreliable networks or across org boundaries.
- Missing or non‑reversible compensations in Sagas.

## Checklist
- Atomicity vs eventual consistency · coordinator failure · compensation design · outbox/inbox

## Sources
- [[distributed-systems/two-phase-commit]] · [[databases/wal]] · [[web/rest-vs-rpc]]

