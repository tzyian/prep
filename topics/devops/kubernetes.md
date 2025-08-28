
# 🚢 Docker (Container Basics)

### 1. What is Docker and why use it?

Docker is a **containerization platform** that packages an application with all its dependencies into a lightweight, portable container.  
**Why**: consistency across environments, fast startup, efficient resource usage compared to VMs.

---

### 2. Difference between container and VM?

- **VM**: runs a full OS on a hypervisor → heavy, slow to boot.
    
- **Container**: shares host kernel, isolates at process level → lightweight, fast, higher density.
    

---

### 3. Key Docker components?

- **Docker Engine**: runs containers.
    
- **Dockerfile**: defines image build steps.
    
- **Image**: immutable snapshot used to create containers.
    
- **Container**: running instance of an image.
    
- **Registry (Docker Hub, ECR, GCR)**: stores images.
    

---

### 4. What is a Dockerfile? Common instructions?

A text file with build instructions. Common ones:

- `FROM` (base image),
    
- `RUN` (execute commands at build time),
    
- `COPY/ADD` (add files),
    
- `CMD/ENTRYPOINT` (default container process),
    
- `EXPOSE` (document ports),
    
- `ENV` (environment variables).
    

---

### 5. What is a multi-stage build?

Building in multiple stages to produce a smaller final image, e.g., compile in Golang builder image, copy binary into scratch/alpine.  
→ reduces image size, keeps runtime clean.

---

### 6. How to persist data in Docker?

Use **volumes** or **bind mounts**; container filesystem is ephemeral.

---

### 7. How does Docker networking work?

Docker creates a default **bridge network**. Containers get private IPs and can talk to each other by name. You can also use host, overlay, or custom networks.

---

---

# ☸️ Kubernetes (Container Orchestration)

### 1. What is Kubernetes?

An **orchestrator** for containers. Handles scheduling, scaling, self-healing, networking, service discovery, and storage management.

---

### 2. Difference between Deployment, ReplicaSet, StatefulSet, DaemonSet?

- **ReplicaSet**: ensures N pods run.
    
- **Deployment**: manages ReplicaSets (supports rolling updates, rollbacks).
    
- **StatefulSet**: for stateful apps (stable identity, persistent storage).
    
- **DaemonSet**: runs one pod per node (logs, monitoring agents).
    

---

### 3. What is a Pod?

The smallest deployable unit in K8s. A pod wraps **one or more containers** that share network namespace and volumes.

---

### 4. What is a Service in Kubernetes?

An abstraction that exposes a set of pods (via label selectors) as a **network service**. Types:

- **ClusterIP** (default, internal only),
    
- **NodePort** (exposes on node IP/port),
    
- **LoadBalancer** (provisions cloud LB),
    
- **Headless** (`clusterIP: None`, used with StatefulSets).
    

---

### 5. What is the difference between ConfigMap and Secret?

- **ConfigMap**: stores non-sensitive configs (env vars, files).
    
- **Secret**: stores sensitive data (passwords, tokens) base64-encoded; mounted with tighter permissions.
    

---

### 6. Explain PersistentVolume (PV) vs PersistentVolumeClaim (PVC)?

- **PV**: cluster resource that represents storage.
    
- **PVC**: namespaced request for storage by a pod.
    
- **StorageClass**: defines how to dynamically provision PVs.
    

---

### 7. Control plane vs Data plane?

- **Control plane**: kube-apiserver, etcd, scheduler, controller-manager → makes cluster decisions.
    
- **Data plane**: kubelet, kube-proxy, container runtime on nodes → actually runs containers.
    

---

### 8. What is kubelet?

An agent running on each node. Ensures containers described in PodSpecs are running and healthy, and reports node/pod status to the API server.

---

### 9. How does Kubernetes handle scaling?

- **Horizontal scaling**: increase/decrease replica count (`kubectl scale` or HPA).
    
- **Vertical scaling**: adjust CPU/memory requests/limits.
    
- **Cluster autoscaler**: adds/removes worker nodes.
    

---

### 10. How does rolling update / rollback work?

- Deployment creates a new ReplicaSet and gradually scales it up while scaling down the old one.
    
- If something fails, `kubectl rollout undo` reverts to previous ReplicaSet.
    

---

### 11. What is a Namespace?

A logical partition in the cluster for resources. Used for multi-tenancy, RBAC, resource quotas.

---

### 12. What is etcd?

Distributed key-value store that stores all cluster state (Pods, Deployments, Secrets, ConfigMaps, etc.). Highly available and consistent.

---

### 13. What is Ingress?

API object that manages **external access** (usually HTTP/HTTPS) to services. Works with Ingress Controller (nginx, traefik, etc.).

---

### 14. How do you debug a pod that won’t start?

- `kubectl describe pod <pod>` → check events.
    
- `kubectl logs <pod> -c <container>` → container logs.
    
- `kubectl exec -it <pod> -- sh` → get inside.
    
- Check PVCs, ConfigMaps, readiness/liveness probes.
    

---

### 15. What are liveness, readiness, and startup probes?

- **Liveness**: is container alive? restart if fails.
    
- **Readiness**: is container ready to receive traffic? controls service endpoints.
    
- **Startup**: delays liveness checks until app has initialized.