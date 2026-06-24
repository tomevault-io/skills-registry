---
name: kubernetes-rollback-runbook
description: Executes structured Kubernetes rollback procedures using kubectl and the kubernetes/client-go library. Monitors rollout status via the apps/v1 Deployment API and triggers PagerDuty incidents through the PagerDuty Events API v2. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Rollback Runbook

Executes structured Kubernetes rollback procedures using kubectl and the kubernetes/client-go library. Monitors rollout status via the apps/v1 Deployment API and triggers PagerDuty incidents through the PagerDuty Events API v2.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-rollback-runbook-3/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
