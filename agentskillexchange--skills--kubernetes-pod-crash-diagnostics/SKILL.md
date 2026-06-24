---
name: kubernetes-pod-crash-diagnostics
description: Runs kubectl describe pod, kubectl logs --previous, and kubectl get events to diagnose CrashLoopBackOff and OOMKilled pods. Parses container exit codes, resource limits, and liveness probe configurations for root cause analysis.
metadata:
  author: agentskillexchange
---

# Kubernetes Pod Crash Diagnostics

Runs kubectl describe pod, kubectl logs --previous, and kubectl get events to diagnose CrashLoopBackOff and OOMKilled pods. Parses container exit codes, resource limits, and liveness probe configurations for root cause analysis.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-pod-crash-diagnostics-3/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
