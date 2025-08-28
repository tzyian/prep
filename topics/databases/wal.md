# Write-Ahead Logging (WAL)

## Question
What is WAL and why is it used?

## Short Answer
WAL records intended changes to a durable log before applying them to data pages so the system can recover to a consistent state after crashes using redo (and sometimes undo) records.

## Deep Dive
- Force log to disk before commit (fsync); pages can be flushed later.
- Checkpointing reduces recovery time; log sequence numbers (LSNs) order writes.
- ARIES: steal/no‑force buffer policy enables high throughput with redo/undo logging.

## Follow‑ups
- Relation to [[aries-recovery]] and 2PC commit records.

