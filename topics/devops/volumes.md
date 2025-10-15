6. Explain PersistentVolume (PV) vs PersistentVolumeClaim (PVC)?

- **PV**: cluster resource that represents storage.
    
- **PVC**: namespaced request for storage by a pod.
    
- **StorageClass**: defines how to dynamically provision PVs.




RWO
RWX


DaemonSet for monitoring

### Operators
[](https://github.com/bregman-arie/devops-exercises/blob/master/topics/kubernetes/README.md#operators)

What is an Operator?  
**

Explained [here](https://kubernetes.io/docs/concepts/extend-kubernetes/operator)

"Operators are software extensions to Kubernetes that make use of custom resources to manage applications and their components. Operators follow Kubernetes principles, notably the control loop."

**

**In simpler words, you can think about an operator as a custom control loop in Kubernetes.**

Why do we need Operators?  
**

The process of managing stateful applications in Kubernetes isn't as straightforward as managing stateless applications where reaching the desired status and upgrades are both handled the same way for every replica. In stateful applications, upgrading each replica might require different handling due to the stateful nature of the app, each replica might be in a different status. As a result, we often need a human operator to manage stateful applications. Kubernetes Operator is suppose to assist with this.

**

**This also help with automating a standard process on multiple Kubernetes clusters**

### Ingress
**From Kubernetes docs: "Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource."**

What is Ingress Controller?  
An implementation for Ingress. It's basically another pod (or set of pods) that does evaluates and processes Ingress rules and this it manages all the redirections.

**There are multiple Ingress Controller implementations (the one from Kubernetes is Kubernetes Nginx Ingress Controller).**

What are some use cases for using Ingress?  
**- Multiple sub-domains (multiple host entries, each with its own service)**- **One domain with multiple services (multiple paths where each one is mapped to a different service/application)**

### Services

[`ClusterIP`](https://kubernetes.io/docs/concepts/services-networking/service/#type-clusterip)

Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default that is used if you don't explicitly specify a `type` for a Service. You can expose the Service to the public internet using an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) or a [Gateway](https://gateway-api.sigs.k8s.io/).

[`NodePort`](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport)

Exposes the Service on each Node's IP at a static port (the `NodePort`). To make the node port available, Kubernetes sets up a cluster IP address, the same as if you had requested a Service of `type: ClusterIP`.

[`LoadBalancer`](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer)

Exposes the Service externally using an external load balancer. Kubernetes does not directly offer a load balancing component; you must provide one, or you can integrate your Kubernetes cluster with a cloud provider.

[`ExternalName`](https://kubernetes.io/docs/concepts/services-networking/service/#externalname)

Maps the Service to the contents of the `externalName` field (for example, to the hostname `api.foo.bar.example`). The mapping configures your cluster's DNS server to return a `CNAME` record with that external hostname value. No proxying of any kind is set up.