---
name: kubernetes-pod-diagnostic-runbook
description: Automated K8s pod troubleshooting using kubectl, crictl, and the Kubernetes API. Runs diagnostic sequences for CrashLoopBackOff, ImagePullBackOff, OOMKilled, and pending pod states. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Pod Diagnostic Runbook

Automated K8s pod troubleshooting using kubectl, crictl, and the Kubernetes API. Runs diagnostic sequences for CrashLoopBackOff, ImagePullBackOff, OOMKilled, and pending pod states.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-pod-diagnostic-runbook/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
