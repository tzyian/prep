# ARIES Recovery

## Question
Outline ARIES recovery phases and key ideas.

## Short Answer
ARIES uses steal/no‑force buffers with physiological logging. Recovery has three phases: Analysis (rebuild state), Redo (repeat history), Undo (roll back losers) using compensation log records (CLRs).

## Deep Dive
- Page LSN vs log LSN determines idempotent redo.
- Dirty page table and transaction table built during Analysis.
- CLRs record undo actions to make recovery itself crash‑safe.

## Follow‑ups
- How WAL [[wal]] and Strict 2PL simplify recovery.

