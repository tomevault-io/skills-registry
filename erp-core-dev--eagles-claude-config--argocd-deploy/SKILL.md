---
name: argocd-deploy
description: Deploy to Kubernetes via ArgoCD GitOps Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# ArgoCD GitOps Deployment

Deploy and manage Kubernetes applications via ArgoCD.

## What To Do

1. **CLI commands**:
   ```bash
   argocd app sync matching-engine
   argocd app get matching-engine
   argocd app diff matching-engine
   argocd app rollback matching-engine <revision>
   ```

2. **Create ArgoCD Application manifest** (deploy/argocd-app.yaml)

3. **Sync policies**: automated prune + selfHeal for production

## Arguments
- `<app-name>`: ArgoCD application name
- `--sync`: Force sync now
- `--prune`: Remove orphaned resources

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
