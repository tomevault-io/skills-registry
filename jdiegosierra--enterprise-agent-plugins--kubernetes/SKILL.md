---
name: kubernetes
description: > Use when this capability is needed.
metadata:
  author: jdiegosierra
---

# Kubernetes Best Practices

## Workload configuration

### Pod resource management
- Always set resource requests AND limits
- Use `resources.requests` for scheduling, `resources.limits` for throttling
- Start conservative and tune based on metrics

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi
```

### Health checks
- **livenessProbe** — restart if unhealthy (use for deadlock detection)
- **readinessProbe** — remove from service if not ready (use for startup/dependency checks)
- **startupProbe** — delay liveness checks during startup (use for slow-starting apps)

### Pod disruption budgets
- Always define PDBs for production workloads
- `minAvailable: 1` or `maxUnavailable: 1` for small deployments

## Troubleshooting

### Pod not starting
1. `kubectl describe pod <name>` — check Events section
2. `kubectl logs <name> --previous` — check crash logs
3. Common causes: image pull errors, resource limits, missing secrets

### Service not reachable
1. `kubectl get endpoints <service>` — verify endpoints exist
2. `kubectl get pods -l <selector>` — check pod readiness
3. `kubectl exec -it <pod> -- curl localhost:<port>/health` — test from inside

### OOMKilled
1. Check `kubectl describe pod` for last termination reason
2. Increase memory limits
3. Profile the application for memory leaks

## Security

- Use NetworkPolicies to restrict pod-to-pod traffic
- Never run containers as root — use `securityContext.runAsNonRoot: true`
- Use ServiceAccounts with minimal RBAC permissions
- Scan images for vulnerabilities before deploying

---
> Source: [jdiegosierra/enterprise-agent-plugins](https://github.com/jdiegosierra/enterprise-agent-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
