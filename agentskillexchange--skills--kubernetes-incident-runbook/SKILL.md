---
name: kubernetes-incident-runbook
description: Executes structured incident response procedures for Kubernetes clusters using kubectl, kube-state-metrics, and the Kubernetes Events API. Automates pod crash diagnosis, OOMKill analysis, and node pressure triage. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Incident Runbook

Executes structured incident response procedures for Kubernetes clusters using kubectl, kube-state-metrics, and the Kubernetes Events API. Automates pod crash diagnosis, OOMKill analysis, and node pressure triage.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-incident-runbook/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
