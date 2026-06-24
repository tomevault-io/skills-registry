---
name: gitops-sync
description: Sync manifests from a git repository to clusters Use when this capability is needed.
metadata:
  author: kubestellar
---

## Your task

Sync manifests from a git repository to Kubernetes clusters.

1. Ask the user for the git repository URL (if not specified via arguments)
2. Optionally preview changes first using `preview_changes`
3. Use `sync_from_git` to apply all manifests to target clusters
4. Report created/updated/unchanged counts per cluster

### Parameters

- `repo`: Git repository URL (required)
- `path`: Path within repo (optional)
- `branch`: Branch name (default: main)
- `clusters`: Target clusters (all if not specified)
- `dry_run`: Set to true to preview without applying

### Available Tools

- `sync_from_git` - Apply manifests from git to clusters
- `preview_changes` - Dry-run to see what would change
- `reconcile` - Force sync to bring clusters in line with git

### Examples

- "Sync my manifests from github.com/myorg/k8s-manifests"
- "Apply the production manifests from git to all clusters"
- "Sync from git repo github.com/org/configs path environments/prod"
- "Preview what would change if I sync from git"

Do not use any other tools besides the kubestellar-deploy MCP tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubestellar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
