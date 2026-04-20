6. Explain PersistentVolume (PV) vs PersistentVolumeClaim (PVC)

- **PV**: cluster resource that represents storage.
- **PVC**: namespaced request for storage by a pod.
- **StorageClass**: defines how to dynamically provision PVs.

Access modes:

- `RWO` (ReadWriteOnce)
- `RWX` (ReadWriteMany)

DaemonSet for monitoring

### Operators

Explained [here](https://kubernetes.io/docs/concepts/extend-kubernetes/operator)

Operators are software extensions to Kubernetes that make use of custom resources to manage applications and their components. Operators follow Kubernetes principles, notably the control loop.

In simpler words, an Operator is a custom control loop in Kubernetes that automates operational tasks for stateful applications.

Why do we need Operators?

Managing stateful applications isn't as straightforward as managing stateless ones. Upgrading replicas or restoring state can require custom logic; Operators codify that logic and automate it across clusters.

### Ingress

From Kubernetes docs: "Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource."

An Ingress Controller is a component (usually running as pods) that evaluates and implements Ingress rules.

Use cases:

- Multiple sub-domains (each with its own service)
- One domain with multiple services (paths mapped to different services)

### Services

[ClusterIP](https://kubernetes.io/docs/concepts/services-networking/service/#type-clusterip)

Exposes the Service on a cluster-internal IP; reachable only within the cluster. Use an Ingress or Gateway for external access.

[NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport)

Exposes the Service on each Node's IP at a static port (`NodePort`). Kubernetes also creates a ClusterIP for the Service.

[LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)

Exposes the Service externally using a cloud provider load balancer.

[ExternalName](https://kubernetes.io/docs/concepts/services-networking/service/#externalname)

Maps the Service to the hostname in `externalName` via a DNS CNAME; no proxying performed.