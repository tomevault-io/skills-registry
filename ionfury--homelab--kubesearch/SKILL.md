---
name: kubesearch
description: | Use when this capability is needed.
metadata:
  author: ionfury
---

# KubeSearch - Homelab Helm Configuration Research

Search kubesearch.dev to find real-world Helm configurations from other homelab repositories.

## Workflow

**Step 1 — Find chart:** `WebFetch https://kubesearch.dev/?search=<chart-name>` → list matching releases with registry paths.

**Step 2 — Get repository links:** Convert registry path to URL format (replace `/` with `-`), then `WebFetch https://kubesearch.dev/hr/<url-path>` → list repositories with direct GitHub links to HelmRelease files.

Examples of the URL conversion:
- `ghcr.io/grafana-helm-charts/grafana` → `ghcr.io-grafana-helm-charts-grafana`
- `charts.longhorn.io/longhorn` → `charts.longhorn.io-longhorn`

**Step 3 — Fetch configs:** Convert GitHub blob URLs to raw URLs, then fetch 3-5 in parallel:
- Blob: `github.com/<owner>/<repo>/blob/<branch>/<path>`
- Raw: `raw.githubusercontent.com/<owner>/<repo>/<branch>/<path>`

**Selection criteria:** recent activity (within 6 months), higher star count, similar chart version, similar infrastructure goals (bare-metal, GitOps, Talos).

## Common Homelab Repositories

| Repository | Focus |
|------------|-------|
| `onedr0p/home-ops` | Flux GitOps, extensive automation |
| `bjw-s/home-ops` | App-template patterns |
| `buroa/k8s-gitops` | Talos + Flux |
| `mirceanton/home-ops` | Well-documented configs |

See [references/output-format.md](references/output-format.md) for the standard output structure when presenting findings.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ionfury) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
