---
name: kubernetes-diagnostics-agent
description: Performs deep cluster troubleshooting using the Kubernetes API server /debug/pprof endpoints and kubectl-debug ephemeral containers. Analyzes resource pressure via the Metrics Server API and kube-state-metrics. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Diagnostics Agent

Performs deep cluster troubleshooting using the Kubernetes API server /debug/pprof endpoints and kubectl-debug ephemeral containers. Analyzes resource pressure via the Metrics Server API and kube-state-metrics.

## Installation

Use the upstream install or setup path that matches your environment:
- git clone https://github.com/kubernetes/kubernetes
- make
- make quick-release

Requirements and caveats from upstream:
- ##### You have a working [Docker environment].
- [Docker environment]: https://docs.docker.com/engine

- Source: https://github.com/kubernetes/kubernetes
- Extracted from upstream docs: https://raw.githubusercontent.com/kubernetes/kubernetes/HEAD/README.md

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-diagnostics-agent/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
