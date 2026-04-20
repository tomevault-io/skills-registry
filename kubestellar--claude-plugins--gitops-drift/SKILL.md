---
name: gitops-drift
description: Detect drift between git manifests and what's deployed in clusters Use when this capability is needed.
metadata:
  author: kubestellar
---

## Your task

Compare a git repository against cluster state to find configuration drift.

1. Ask the user for the git repository URL (if not specified via arguments)
2. Use `detect_drift` to compare git manifests against cluster state
3. Present findings showing:
   - Missing resources (in git but not in cluster)
   - Modified resources (differ between git and cluster)
   - Specific field differences

### Parameters

- `repo`: Git repository URL (required)
- `path`: Path within repo (optional)
- `branch`: Branch name (default: main)
- `clusters`: Target clusters (all if not specified)

### Examples

- "Are my clusters in sync with git?"
- "Check for drift from github.com/myorg/manifests"
- "What's different between git and production?"
- "Find configuration drift in my clusters"

Do not use any other tools besides the kubestellar-deploy MCP tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubestellar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
