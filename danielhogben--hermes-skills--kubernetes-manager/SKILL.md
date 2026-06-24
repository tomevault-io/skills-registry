---
name: kubernetes-manager
description: Kubernetes cluster management skill. Use when: managing k8s deployments, Use when this capability is needed.
metadata:
  author: Danielhogben
---

# Kubernetes Manager

Manage Kubernetes clusters via kubectl and helm. Supports deployments, scaling, pod management, log viewing, namespace operations, rollouts, secrets, ingress, and ConfigMap management.

## Usage

```bash
python3 kubernetes_manager.py <command> [args...]
```

## Commands

| Command | Description |
|---------|-------------|
| `status` | Show cluster overview (nodes, namespaces, resource usage) |
| `deploy` | Create or update a deployment |
| `scale` | Scale a deployment to N replicas |
| `pods` | List pods with status and restart counts |
| `logs` | Stream or fetch pod logs |
| `helm` | List or search helm releases |
| `namespace` | Create/delete/list namespaces |
| `rollout` | Manage rollout history and status |
| `secrets` | Manage Kubernetes secrets |
| `ingress` | List and inspect ingress resources |
| `config` | Manage ConfigMaps |

## Configuration

No persistent configuration required. Requires `kubectl` and optionally `helm` installed and configured.

---
> Source: [Danielhogben/hermes-skills](https://github.com/Danielhogben/hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
