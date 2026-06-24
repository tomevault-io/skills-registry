---
name: check-secrets
description: Verify secrets and configmaps in a namespace. Use for deployment config verification, debugging missing env vars, or auditing secret presence. Use when this capability is needed.
metadata:
  author: dmzoneill
---

# Check Secrets

Verify secrets and configmaps are properly configured in a namespace.

## Inputs

| Input | Type | Default | Purpose |
|-------|------|---------|---------|
| `namespace` | string | required | Kubernetes namespace |
| `environment` | string | stage | stage, production, ephemeral |
| `deployment` | string | "" | Optional: specific deployment to check references |

## Workflow

### 1. Load Persona
- `persona_load("devops")`

### 2. List Resources
- `kubectl_get_secrets(namespace="{{ namespace }}", environment="{{ environment }}")`
- `kubectl_get_configmaps(namespace="{{ namespace }}", environment="{{ environment }}")`

### 3. Check Deployment References (if deployment specified)
- `kubectl_describe_deployment(deployment="{{ deployment }}", namespace="{{ namespace }}", environment="{{ environment }}")`
- Parse for `secretKeyRef`, `configMapKeyRef`, `secretRef`, `configMapRef`
- Compare referenced names to existing secrets/configmaps

### 4. Report Missing
- If deployment references missing: list missing secrets and configmaps

### 5. Error Recovery
- "unauthorized" → `kube_login(cluster="stage" or "prod")`
- "no route to host" → `vpn_connect()`

### 6. Memory
- `memory_session_log("Checked secrets and configmaps", "namespace={{ namespace }}, secrets={{ count }}")`

## Output

Report: secrets list (name, type), configmaps list, deployment references (if specified), and any missing resources.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dmzoneill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
