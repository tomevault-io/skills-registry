---
name: kubernetes-pod-crashloop-runbook
description: Automates diagnosis of CrashLoopBackOff pods using kubectl commands wrapped via the Kubernetes API server. Fetches recent events, container logs, and resource quota status to identify root causes such as OOMKilled, misconfigured liveness probes, or missing ConfigMaps. Generates a step-by-step remediation runbook.
metadata:
  author: agentskillexchange
---

# Kubernetes Pod Crashloop Runbook

Automates diagnosis of CrashLoopBackOff pods using kubectl commands wrapped via the Kubernetes API server. Fetches recent events, container logs, and resource quota status to identify root causes such as OOMKilled, misconfigured liveness probes, or missing ConfigMaps. Generates a step-by-step remediation runbook.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-pod-crashloop-runbook/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
