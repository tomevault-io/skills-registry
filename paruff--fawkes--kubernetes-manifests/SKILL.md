---
name: kubernetes-manifests
description: K8s manifest rules for Fawkes — labels, limits, security context. Load when editing platform/ or charts/. Use when this capability is needed.
metadata:
  author: paruff
---

# K8s Manifests — Fawkes

Every deployment needs:

- Labels: `app`, `version`, `component`, `managed-by: fawkes`
- Resource limits (requests + limits)
- Security context: `runAsNonRoot: true`, `readOnlyRootFilesystem: true`
- Liveness + readiness probes
- No `latest` image tags

```yaml
# Standard container block
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false
resources:
  requests: { cpu: 100m, memory: 128Mi }
  limits: { cpu: 500m, memory: 512Mi }
```

Namespaces: `argocd`, `fawkes-platform`, `fawkes-observability`, `fawkes-cicd`, `fawkes-security`, `fawkes-apps`

Validate:

```bash
python -c "import yaml; yaml.safe_load(open('FILE'))"
grep -L "managed-by: fawkes" platform/apps/*/deployment.yaml
grep -L "limits:" platform/apps/*/deployment.yaml
```

---
> Source: [paruff/fawkes](https://github.com/paruff/fawkes) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
