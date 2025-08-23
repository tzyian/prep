# Question Card: Model Serving Patterns

- Question: Compare common model serving patterns (batch, online, streaming, feature store). When to use which?
- Role: ml
- Difficulty: mid

## Short Answer
Batch scoring precomputes predictions for offline use; online serving answers low‑latency requests; streaming scoring applies models to event streams; feature stores centralize offline/online features with consistency guarantees. Choose based on latency and freshness requirements.

## Deep Dive
- Online: autoscaling, cold start, hardware acceleration.
- Streaming: windowed features, backpressure.
- Features: offline/online parity; materialization; point‑in‑time correctness.

## Example
```text
CTR prediction: online model + offline batch re‑ranking
```

## Follow‑ups
- Canarying models; shadow deployments; A/B testing.
- Monitoring: drift, performance decay.

## Pitfalls
- Feature skew; lack of versioning; inconsistent transformations.

## Checklist
- Latency · freshness · parity · scaling · monitoring

## Sources
- Feast, KFServing/Seldon docs

