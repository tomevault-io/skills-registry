---
name: azure-aks
description: description: Azure Kubernetes Service deployment patterns Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: azure-aks
description: Azure Kubernetes Service deployment patterns
---

# Azure AKS

> Kubernetes deployment for Azure.

---

## Deployment Manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myacr.azurecr.io/myapp:v1
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "128Mi"
              cpu: "100m"
            limits:
              memory: "256Mi"
              cpu: "200m"
```

---

## Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer
```

---

## Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: myapp-service
                port:
                  number: 80
```

---

## ConfigMap & Secrets

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  ENVIRONMENT: "production"

---
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
type: Opaque
data:
  CONNECTION_STRING: <base64-encoded>
```

---

## Health Checks

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5

readinessProbe:
  httpGet:
    path: /ready
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 3
```

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Resource limits | Unlimited resources |
| Health checks | Skip probes |
| Rolling updates | Recreate strategy |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
