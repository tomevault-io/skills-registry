---
name: deployment
description: Game server deployment with Docker, Kubernetes, and global distribution strategies Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Game Server Deployment

Deploy **scalable game servers** with containerization and orchestration.

## Docker Production Build

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
RUN addgroup -g 1001 -S app && adduser -S app -u 1001
COPY --from=builder --chown=app:app /app/dist ./dist
COPY --from=builder --chown=app:app /app/node_modules ./node_modules
USER app

HEALTHCHECK --interval=30s --timeout=5s \
  CMD wget -q --spider http://localhost:8080/health || exit 1

EXPOSE 7777/udp 8080/tcp
CMD ["node", "dist/server.js"]
```

## Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: game-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: game-server
  template:
    spec:
      containers:
      - name: game-server
        image: game-server:latest
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "2000m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
```

## Auto-Scaling

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: game-server
  minReplicas: 3
  maxReplicas: 100
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

## Troubleshooting

### Common Failure Modes

| Error | Root Cause | Solution |
|-------|------------|----------|
| CrashLoopBackOff | Startup crash | Check logs, config |
| OOMKilled | Memory exceeded | Increase limits |
| ImagePullBackOff | Auth failed | Check secrets |
| Pending | No resources | Scale cluster |

### Debug Checklist

```bash
# Check pods
kubectl get pods -l app=game-server

# Check logs
kubectl logs -l app=game-server --tail=100

# Check events
kubectl get events --sort-by=.lastTimestamp

# Rollback
kubectl rollout undo deployment/game-server
```

## Unit Test Template

```yaml
# test-deployment.yaml
apiVersion: v1
kind: Pod
metadata:
  name: deployment-test
spec:
  containers:
  - name: test
    image: game-server:test
    command: ["npm", "test"]
  restartPolicy: Never
```

## Resources

- `assets/` - Deployment templates
- `references/` - K8s guides

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
