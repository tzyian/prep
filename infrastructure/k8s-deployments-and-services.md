# Question Card: K8s Deployments and Services

- Question: Explain how Kubernetes Deployments manage rollout and how Services route traffic.
- Role: infra
- Difficulty: mid

## Short Answer
Deployments manage ReplicaSets and rollouts with strategies (rolling update, blue/green via separate Deployment). Services provide stable virtual IPs and load‑balance to Pod endpoints via selectors; ClusterIP, NodePort, and LoadBalancer expose different scopes.

## Deep Dive
- Rollouts: surge/unavailable; rollout history; probes and readiness gates.
- Services: kube-proxy modes (iptables/IPVS); headless Services and DNS for stateful sets.
- Ingress/IngressClass vs Gateway API for L7 routing.

## Example
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate: { maxSurge: 1, maxUnavailable: 0 }
```

## Follow‑ups
- Zero‑downtime with readiness + preStop; canaries.
- Service discovery in multi‑cluster.

## Pitfalls
- Skipping readiness checks; breaking connections on rollout.

## Checklist
- Strategy · probes · service types · kube-proxy · DNS

## Sources
- Kubernetes docs

