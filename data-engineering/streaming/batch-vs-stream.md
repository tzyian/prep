# Question Card: Batch vs Stream Processing

- Question: Contrast batch and stream processing. When do you choose each?
- Role: data-eng
- Difficulty: intro

## Short Answer
Batch processes bounded datasets with high throughput and complex aggregations at higher latency (minutes+). Streaming consumes unbounded data with low latency, windowing, and stateful operators. Choose streaming for near‑real‑time features/monitoring; batch for large backfills and offline analytics.

## Deep Dive
- Time: event time vs processing time; watermarks; late data handling.
- Semantics: exactly‑once via checkpointing/state backends.
- Cost: streaming infra always on; batch is elastic.

## Example
```text
Streaming: 5‑min tumbling window counts with watermark 2m
```

## Follow‑ups
- Lambda vs Kappa architectures; lakehouse unification.
- Choosing Flink vs Spark Structured Streaming.

## Pitfalls
- Ignoring late/out‑of‑order events; incorrect windowing.

## Checklist
- Windows · watermarks · state · backfills · latency budgets

## Sources
- Flink/Spark docs on windows and watermarks

