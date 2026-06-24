---
name: kubernetes-operator
description: > Use when this capability is needed.
metadata:
  author: Abhiram1106
---

## When to activate

Writing K8s manifests, debugging pod issues, setting up Helm charts, RBAC configuration.

## When NOT to activate

- Pure Docker without K8s (use docker-specialist)
- CI/CD pipeline (use devops-orchestrator)

## Minimal production Deployment

**PASS: Complete deployment with all safety features**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: production
  labels: { app: myapp }
spec:
  replicas: 3
  selector:
    matchLabels: { app: myapp }
  template:
    metadata:
      labels: { app: myapp }
    spec:
      containers:
      - name: myapp
        image: myapp:1.2.3          # NEVER use :latest in production
        ports: [{ containerPort: 3000 }]
        resources:
          requests: { cpu: "100m", memory: "128Mi" }
          limits:   { cpu: "500m", memory: "512Mi" }  # prevent OOMKilled
        livenessProbe:
          httpGet: { path: /health, port: 3000 }
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet: { path: /ready, port: 3000 }
          initialDelaySeconds: 5
          periodSeconds: 5
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef: { name: myapp-secrets, key: database-url }
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        readOnlyRootFilesystem: true
```

**FAIL: Dangerous defaults**
```yaml
image: myapp:latest          # No pinned version
# No resources (can OOM and crash cluster)
# No probes (Kubernetes can't detect unhealthy pods)
# No securityContext (runs as root)
```

## Pod crash debugging

```bash
# CrashLoopBackOff
kubectl logs <pod> --previous         # logs before crash
kubectl describe pod <pod>            # events section
kubectl get events --sort-by='.lastTimestamp' | head -20

# ImagePullBackOff
kubectl describe pod <pod>            # look for image pull error
# Fix: check image name/tag, check registry credentials

# OOMKilled
kubectl top pods                      # check memory usage
kubectl describe pod <pod> | grep -A5 "OOM"
# Fix: increase memory limit, fix memory leak

# Pending pod (not scheduled)
kubectl describe pod <pod>            # look for "Insufficient" in events
kubectl get nodes -o wide             # check node capacity
```

## Namespace isolation (RBAC template)

```yaml
apiVersion: v1
kind: ServiceAccount
metadata: { name: myapp, namespace: production }
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata: { name: myapp-role, namespace: production }
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata: { name: myapp-binding, namespace: production }
subjects: [{ kind: ServiceAccount, name: myapp, namespace: production }]
roleRef: { kind: Role, name: myapp-role, apiGroup: rbac.authorization.k8s.io }
```

## Resource sizing guide

| App Type | CPU Request | CPU Limit | Mem Request | Mem Limit |
|----------|-------------|-----------|-------------|-----------|
| Node.js API | 100m | 500m | 128Mi | 512Mi |
| Python API | 100m | 500m | 256Mi | 1Gi |
| Database (Postgres) | 250m | 1000m | 512Mi | 2Gi |
| Redis | 100m | 500m | 128Mi | 512Mi |

## Verification

- [ ] All pods Running (not Pending/CrashLoop)
- [ ] Resource limits set on all containers
- [ ] Liveness + readiness probes configured
- [ ] No containers running as root
- [ ] Secrets mounted from K8s Secrets, not env hardcoded
- [ ] Image tags pinned (not :latest)

---
> Source: [Abhiram1106/omnix](https://github.com/Abhiram1106/omnix) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
