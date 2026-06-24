---
name: kubernetes-crashloop-diagnostician
description: Diagnoses CrashLoopBackOff pods using the Kubernetes client-go API and kubectl debug. Analyzes container exit codes, OOMKill events, and liveness probe failures with automated remediation suggestions. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes CrashLoop Diagnostician

Diagnoses CrashLoopBackOff pods using the Kubernetes client-go API and kubectl debug. Analyzes container exit codes, OOMKill events, and liveness probe failures with automated remediation suggestions.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-crashloop-diagnostician/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
