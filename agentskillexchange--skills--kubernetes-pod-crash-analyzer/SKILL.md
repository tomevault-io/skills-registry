---
name: kubernetes-pod-crash-analyzer
description: Investigates CrashLoopBackOff and OOMKilled pod failures using kubectl and the Kubernetes API. Correlates container logs, event streams, and resource metrics from metrics-server to diagnose root causes automatically. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Pod Crash Analyzer

Investigates CrashLoopBackOff and OOMKilled pod failures using kubectl and the Kubernetes API. Correlates container logs, event streams, and resource metrics from metrics-server to diagnose root causes automatically.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-pod-crash-analyzer-3/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
