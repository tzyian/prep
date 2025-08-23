# Question Card: Containers vs VMs

- Question: Compare containers and virtual machines. When do you choose each?
- Role: infra
- Difficulty: intro

## Short Answer
VMs virtualize hardware and provide strong isolation with separate kernels; containers share the host kernel and isolate via namespaces/cgroups. Containers start faster and are lighter; VMs offer stronger isolation and heterogenous OS kernels.

## Deep Dive
- Security/isolation: kernel attack surface vs hypervisor; gVisor/Firecracker as middle ground.
- Ops: image build (OCI), registry, layering; VM templates and snapshots.
- Networking/storage: CNI, overlay networks; CSI, volumes.

## Example
```text
Firecracker microVMs for multi-tenant serverless isolation
```

## Follow‑ups
- Rootless containers; user namespaces; SELinux/AppArmor.
- Overcommit and cgroup limits.

## Pitfalls
- Assuming container = security boundary; noisy neighbor without limits.

## Checklist
- Isolation level · startup time · density · kernel deps · tenancy

## Sources
- OCI/Docker, Firecracker docs

