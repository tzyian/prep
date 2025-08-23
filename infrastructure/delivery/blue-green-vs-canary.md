# Question Card: Blue-Green vs Canary

- Question: Compare blue-green and canary deployments. When to use which?
- Role: infra
- Difficulty: intro

## Short Answer
Blue‑green runs two production environments and switches all traffic at once; fast rollback but needs double capacity. Canary shifts a small percentage of traffic to a new version, monitors, and gradually ramps up; safer for uncovering issues but more orchestration.

## Deep Dive
- Control planes: service mesh or gateway for traffic splitting.
- Metrics: error rate, latency, saturation; automated rollback.
- State: schema changes must be backwards compatible.

## Example
```text
Canary: 1% → 5% → 25% → 50% → 100% with SLO checks
```

## Follow‑ups
- Feature flags and dark launches.
- Multi‑region rollouts.

## Pitfalls
- Noisy metrics hide regressions; non‑idempotent init scripts.

## Checklist
- Capacity · traffic split · SLO/SLA · rollback plan · schema compat

## Sources
- Kubernetes, Istio/Gateway API docs

