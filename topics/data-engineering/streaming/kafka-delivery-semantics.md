# Question Card: Kafka Delivery Semantics

- Question: Explain at-most-once, at-least-once, and exactly-once processing in Kafka.
- Role: data-eng
- Difficulty: mid

## Short Answer
At-most-once may lose messages; at-least-once may process duplicates; exactly-once ensures each record affects state once. Kafka supports EOS via idempotent producers, transactions, and consumer isolation (read committed) when sink supports transactional writes.

## Deep Dive
- Idempotent producer: sequence numbers; retries without duplication.
- Transactions: producer groups writes to multiple partitions and a sink atomically.
- Consumers: `isolation.level=read_committed`; handling rebalances.

## Example
```properties
enable.idempotence=true
transactional.id=orders-tx
isolation.level=read_committed
```

## Follow‑ups
- End‑to‑end EOS with Kafka Streams/Flink; sinks without transactions.
- Outbox pattern to bridge DB→Kafka.

## Pitfalls
- Assuming EOS across external systems; not fencing old producers.

## Checklist
- Idempotence · transactions · read_committed · fencing · rebalance

## Sources
- Kafka docs on transactions and idempotence
- [[consistency-models]]

