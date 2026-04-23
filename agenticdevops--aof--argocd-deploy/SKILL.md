---
name: argocd-deploy
description: ArgoCD deployment and rollback operations Use when this capability is needed.
metadata:
  author: agenticdevops
---

# ArgoCD Deploy Skill

Manage ArgoCD applications for continuous deployment and GitOps workflows.

## When to Use This Skill

- Deploying new versions
- Syncing application state
- Rolling back deployments
- Checking deployment status
- Managing environments

## Steps

1. **List applications** — `argocd app list`
2. **Sync application** — `argocd app sync app-name`
3. **Check status** — `argocd app get app-name`
4. **Rollback** — `argocd app rollback app-name revision`
5. **Check history** — `argocd app history app-name`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
