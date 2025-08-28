# Question Card: Observability Pillars

- Question: What are the three pillars of observability and how do you design an effective observability stack?
- Role: infra
- Difficulty: intro

## Short Answer
Metrics, logs, and traces together provide visibility into system health and request paths. Effective stacks define SLOs/SLIs, consistent context propagation, cardinality control, and retention/cost policies.

## Deep Dive
- Metrics: RED/USE, histograms (correct buckets), high‑cardinality pitfalls.
- Logs: structured, sampled; correlation IDs.
- Traces: OpenTelemetry, propagation (`traceparent`), spans across services.

## Example
```text
SLI: p95 latency < 200ms, error rate < 1%
```

## Follow‑ups
- Tail‑based sampling; exemplars linking metrics↔traces.
- On‑call runbooks and alerts (rate/ratio errors).

## Pitfalls
- Unbounded labels; missing propagation; alert noise.

## Checklist
- SLO/SLI · context propagation · sampling · retention · runbooks

## Sources
- OpenTelemetry, SRE Workbook

