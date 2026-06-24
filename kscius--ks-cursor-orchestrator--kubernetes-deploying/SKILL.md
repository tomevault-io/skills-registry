---
name: kubernetes-deploying
description: >- Use when this capability is needed.
metadata:
  author: kscius
---

## When to use

- >-.

> **On-demand loading:** Read when deploying to Kubernetes. Skip for non-K8s infra. Prefer `devops-skills` for pre-apply validation only.

# Kubernetes Deploying

Deploy and manage applications on Kubernetes. Manifest examples and ops detail live in `references/`.

## Workflow

1. Define Deployment with requests/limits, liveness + readiness probes
2. Expose via Service; add Ingress when external HTTPS needed
3. ConfigMap/Secret for config — no secrets in git
4. `kubectl apply --dry-run` then apply; see `references/operations.md`
5. Verify rollout: `kubectl rollout status`

## References

| File | Contents |
|------|----------|
| `references/manifest-examples.md` | Deployment, Service, Ingress, ConfigMap, HPA YAML |
| `references/operations.md` | kubectl commands, strategies, health checks, tips |

## Related skills

- `devops-skills` — Terraform/compose/K8s dry-run patterns
- `setting-up-terraform` — cluster infra (not app manifests)

---
> Source: [kscius/KS-Cursor-Orchestrator](https://github.com/kscius/KS-Cursor-Orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
