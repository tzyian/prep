# Question Card: Exactly-Once Processing

- Question: What does “exactly-once processing” mean and how is it achieved end-to-end?
- Role: data-eng
- Difficulty: advanced

## Short Answer
It means each input affects downstream state once even across retries and failures. Achieved via idempotent writes, transactional sinks/sources, and atomic commit of read position with output (e.g., Kafka transactions + streams). Without transactional sinks, emulate with dedupe keys and idempotent upserts.

## Deep Dive
- Source: read‑committed + checkpointed offsets.
- Processing: deterministic ops; state stores with snapshots.
- Sink: upsert with natural keys; two‑phase commit connectors or outbox pattern.

## Example
```text
Kafka Streams: source(tx) → process → sink(tx), commit both or none
```

## Follow‑ups
- Idempotency keys schema; replay after partial failure.
- External DB sinks without transactions.

## Pitfalls
- Claiming EOS where sinks can duplicate; not fencing writers after failover.

## Checklist
- Offsets+output atomicity · idempotent upserts · checkpoints · fencing

## Sources
- Kafka Streams, Flink checkpoints docs

