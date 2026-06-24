---
name: kubernetes-diagnostic-runbook
description: Executes diagnostic workflows against Kubernetes clusters using kubectl and the Kubernetes Python client (kubernetes.client). Checks pod health, resource quotas, event logs, and node conditions for rapid incident triage. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Diagnostic Runbook

Executes diagnostic workflows against Kubernetes clusters using kubectl and the Kubernetes Python client (kubernetes.client). Checks pod health, resource quotas, event logs, and node conditions for rapid incident triage.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-diagnostic-runbook/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
