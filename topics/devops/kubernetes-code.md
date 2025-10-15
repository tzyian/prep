
```yaml
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: demo

---
# Redis headless Service (stable network IDs for StatefulSet)
apiVersion: v1
kind: Service
metadata:
  name: redis
  namespace: demo
spec:
  clusterIP: None
  selector:
    app: redis
  ports:
    - name: redis
      port: 6379
      targetPort: 6379

---
# Redis StatefulSet (1 replica with persistent storage)
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
  namespace: demo
spec:
  serviceName: redis
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
        - name: redis
          image: redis:7.2
          args: ["--appendonly", "yes"]
          ports:
            - containerPort: 6379
              name: redis
          volumeMounts:
            - name: data
              mountPath: /data
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 5Gi
        # storageClassName: gp3   # <- set if you use a specific StorageClass

---
# Backend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: demo
spec:
  replicas: 2
  selector:
    matchLabels: { app: backend }
  template:
    metadata:
      labels: { app: backend }
    spec:
      containers:
        - name: backend
          image: ghcr.io/example/backend:1.0.0
          ports:
            - containerPort: 8080
          env:
            - name: REDIS_ADDR
              value: redis.demo.svc.cluster.local:6379
          readinessProbe:
            httpGet: { path: /healthz, port: 8080 }
            initialDelaySeconds: 5
            periodSeconds: 5

---
# Backend Service (ClusterIP)
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
  namespace: demo
spec:
  selector: { app: backend }
  ports:
    - name: http
      port: 8080
      targetPort: 8080

---
# Frontend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: demo
spec:
  replicas: 2
  selector:
    matchLabels: { app: frontend }
  template:
    metadata:
      labels: { app: frontend }
    spec:
      containers:
        - name: frontend
          image: ghcr.io/example/frontend:1.0.0
          ports:
            - containerPort: 3000
          env:
            - name: API_BASE
              value: "http://backend-svc.demo.svc.cluster.local:8080"

---
# Frontend Service (ClusterIP)
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
  namespace: demo
spec:
  selector: { app: frontend }
  ports:
    - name: http
      port: 3000
      targetPort: 3000

---
# NGINX Ingress (external access)
# Requires nginx Ingress Controller installed in the cluster.
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: demo
  annotations:
    kubernetes.io/ingress.class: nginx
    # If your backend expects paths without the /api prefix, uncomment:
    # nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: frontend-svc
                port:
                  number: 3000
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: backend-svc
                port:
                  number: 8080
  # TLS (optional): terminate HTTPS at Ingress
  # tls:
  #   - hosts: [app.example.com]
  #     secretName: app-example-com-tls

```