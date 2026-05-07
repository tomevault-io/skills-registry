---
name: sync-to-prod
description: This skill should be used when users need to sync/promote configuration from staging (aws-staging) to production (aws-prod) environment. It handles image tag synchronization, identifies configuration differences, and manages the promotion workflow. Triggers on requests mentioning "sync to prod", "promote to production", "update prod images", or comparing staging vs production. Use when this capability is needed.
metadata:
  author: neversight
---

# Sync to Production Skill

This skill provides workflows for synchronizing Kubernetes kustomization configurations from staging to production environment in the simplex-gitops repository.

## ⚠️ CRITICAL: Production Deployment Policy

**生产环境部署必须手动执行，禁止自动同步。**

The workflow is:
1. ✅ Update kustomization.yaml (can be automated)
2. ✅ Commit and push to GitLab (can be automated)
3. ⛔ **ArgoCD sync to production cluster - MUST BE MANUAL**

After pushing changes, inform the user:
- Changes are pushed to the repository
- Production ArgoCD app will detect the changes but **will NOT auto-sync**
- User must manually trigger sync via ArgoCD UI or CLI when ready

```bash
# View pending changes (safe, read-only)
argocd app get simplex-aws-prod
argocd app diff simplex-aws-prod

# Manual sync (ONLY when user explicitly requests)
argocd app sync simplex-aws-prod
```

**NEVER run `argocd app sync simplex-aws-prod` automatically.**

## File Locations

```
kubernetes/overlays/aws-staging/kustomization.yaml  # Staging config
kubernetes/overlays/aws-prod/kustomization.yaml     # Production config
```

## Quick Commands

### View Image Differences

```bash
# Using the sync script
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --diff

# Or using make target (if in kubernetes/ directory)
make compare-images
```

### Sync Images

```bash
# Sync specific services
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --images front,anotherme-agent

# Sync all images (dry-run first)
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --all --dry-run

# Sync all images (apply changes)
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --all
```

## Sync Workflow

### Step 1: Compare Environments

Run the diff command to see what's different between staging and production:

```bash
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --diff
```

This shows:
- 🔄 **DIFFERENT TAGS**: Services with different versions
- ✅ **SAME TAGS**: Services already in sync
- ⚠️ **STAGING ONLY**: Services only in staging
- ⚠️ **PROD ONLY**: Services only in production

### Step 2: Review and Select Services

Decide which services to promote. Common patterns:

```bash
# Promote a single critical service
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --images front --dry-run

# Promote frontend services
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --images front,front-homepage --dry-run

# Promote all AI services
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --images anotherme-agent,anotherme-api,anotherme-search,anotherme-worker --dry-run

# Promote everything
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --all --dry-run
```

### Step 3: Apply Changes

After reviewing dry-run output, apply the changes:

```bash
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --images <services>
```

### Step 4: Commit and Push

```bash
cd /path/to/simplex-gitops
git add kubernetes/overlays/aws-prod/kustomization.yaml
git commit -m "chore: promote <services> to production"
git push
```

**重要：推送后 ArgoCD 会检测到变更，但不会自动同步到生产集群。**

### Step 5: Manual Production Sync (User Action Required)

推送完成后，需要用户手动触发生产环境同步：

```bash
# 查看待同步的变更
argocd app get simplex-aws-prod
argocd app diff simplex-aws-prod

# 用户确认后手动同步
argocd app sync simplex-aws-prod
```

或通过 ArgoCD Web UI 手动点击 Sync 按钮：
- URL: http://192.168.10.117:31006
- 找到 `simplex-aws-prod` 应用
- 点击 "SYNC" 按钮

## Configuration Sections That May Need Sync

Beyond image tags, these sections may differ between environments:

### 1. Image Tags (Primary Sync Target)

Located in the `images:` section. This is what the sync script handles.

### 2. ConfigMap Patches

Files in `patches/` directory may contain environment-specific values:

| Patch File | Purpose | Sync Consideration |
|------------|---------|-------------------|
| `api-cm0-configmap.yaml` | API config | Usually environment-specific, don't sync |
| `gateway-cm0-configmap.yaml` | Gateway config | Usually environment-specific |
| `anotherme-agent-env-configmap.yaml` | Agent config | May need selective sync |
| `anotherme-agent-secrets.yaml` | Agent secrets | Never sync, environment-specific |
| `anotherme-search-env-configmap.yaml` | Search config | May need selective sync |
| `simplex-cron-env-configmap.yaml` | Cron config | Usually environment-specific |
| `simplex-router-cm0-configmap.yaml` | Router config | Usually environment-specific |
| `frontend-env.yaml` | Frontend env vars | Usually environment-specific |
| `ingress.yaml` | Ingress rules | Never sync, different domains |

### 3. Replica Counts

Staging often runs with fewer replicas. Production uses base defaults or higher. This is intentional and should NOT be synced.

### 4. Node Pool Assignments

- Staging: `karpenter.sh/nodepool: staging` / `singleton-staging`
- Production: `karpenter.sh/nodepool: production` / `singleton-production`

These are environment-specific and should NOT be synced.

### 5. Storage Classes

Both environments use similar patterns but production uses `gp3` while staging uses `ebs-gp3-auto`. Usually no sync needed.

### 6. High Availability Settings

Production has additional HA configurations:
- `topologySpreadConstraints` for cross-AZ distribution
- `terminationGracePeriodSeconds: 60` for graceful shutdown

These are production-specific optimizations and should NOT be synced to staging.

## Manual Sync Patterns

For configurations not handled by the script:

### Sync a Specific ConfigMap Patch

```bash
# Compare
diff kubernetes/overlays/aws-staging/patches/anotherme-agent-env-configmap.yaml \
     kubernetes/overlays/aws-prod/patches/anotherme-agent-env-configmap.yaml

# Copy if needed (carefully review first!)
cp kubernetes/overlays/aws-staging/patches/anotherme-agent-env-configmap.yaml \
   kubernetes/overlays/aws-prod/patches/anotherme-agent-env-configmap.yaml
```

### Sync New Resources

If staging has new resources (PV, PVC, etc.) that production needs:

1. Check staging `resources:` section for new entries
2. Copy the resource files to aws-prod
3. Add to aws-prod `kustomization.yaml` resources section
4. Adjust environment-specific values (namespace, labels, etc.)

## Verification After Sync

### Check ArgoCD Status (Read-Only, Safe)

```bash
# 查看应用状态和待同步变更
argocd app get simplex-aws-prod
argocd app diff simplex-aws-prod
```

### Manual Sync (User Must Explicitly Request)

```bash
# ⛔ 仅在用户明确要求时执行
argocd app sync simplex-aws-prod
```

### Check Deployed Versions

```bash
# Production namespace
k1 get pods -n production -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].image}{"\n"}{end}'

# Staging namespace
k2 get pods -n staging -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].image}{"\n"}{end}'
```

### Validate Manifests

```bash
kubectl kustomize kubernetes/overlays/aws-prod > /tmp/prod-manifests.yaml
kubectl kustomize kubernetes/overlays/aws-staging > /tmp/staging-manifests.yaml
diff /tmp/staging-manifests.yaml /tmp/prod-manifests.yaml
```

## Troubleshooting

### Script Not Finding Repository

Ensure you're in the simplex-gitops directory or set the path explicitly:

```bash
cd /path/to/simplex-gitops
python3 ~/.claude/skills/sync-to-prod/scripts/sync_images.py --diff
```

### Image Not Found in Staging

The service may use a different image name format (Aliyun vs ECR). Check both formats in the kustomization files.

### ArgoCD Not Syncing

```bash
# 查看应用状态（只读）
argocd app get simplex-aws-prod --show-operation

# 刷新应用检测最新变更（只读，安全）
argocd app refresh simplex-aws-prod

# ⛔ 手动同步 - 仅在用户明确要求时执行
argocd app sync simplex-aws-prod
```

## Service Categories Reference

| Category | Services |
|----------|----------|
| AI Core | `anotherme-agent`, `anotherme-api`, `anotherme-search`, `anotherme-worker` |
| Frontend | `front`, `front-homepage` |
| Backend | `simplex-cron`, `simplex-gateway-api`, `simplex-gateway-worker` |
| Data | `data-search-api`, `crawler` |
| Infrastructure | `litellm`, `node-server`, `simplex-router`, `simplex-router-backend`, `simplex-router-fronted` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
