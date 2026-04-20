---
name: deploy
description: Deploy an app to multiple clusters with smart placement Use when this capability is needed.
metadata:
  author: kubestellar
---

## Your task

Deploy a workload to clusters. You can specify target clusters explicitly or let kubestellar-deploy find matching clusters based on requirements.

1. Get the manifest from the user or help them create one
2. Ask about target clusters or placement requirements (GPU, memory, CPU)
3. Use `deploy_app` to deploy the manifest

### Parameters

- `manifest`: The Kubernetes YAML manifest
- `clusters`: Optional list of target clusters
- `gpu_type`: Optional GPU type requirement (e.g., "nvidia.com/gpu")
- `min_gpu`: Optional minimum GPU count
- `dry_run`: Set to true to preview without applying

### Smart Placement

If the user doesn't specify clusters, kubestellar-deploy can:
- Deploy to all clusters
- Filter to clusters with specific GPU types
- Filter to clusters with minimum CPU/memory
- Filter to clusters with specific node labels

Use `find_clusters_for_workload` and `list_cluster_capabilities` to help with placement decisions.

### Examples

- "Deploy this nginx deployment to all clusters"
- "Deploy my ML model to clusters with GPUs"
- "Deploy this app to clusters with at least 16Gi memory"
- "Do a dry run of deploying this manifest"

Do not use any other tools besides the kubestellar-deploy MCP tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kubestellar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
