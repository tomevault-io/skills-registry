---
name: kubernetes-crashloopbackoff-resolver
description: Diagnoses CrashLoopBackOff pods using the Kubernetes API /api/v1/pods endpoint, kubectl logs --previous, and container runtime inspection via crictl. Identifies OOMKilled events, missing ConfigMaps, and image pull failures. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes CrashLoopBackOff Resolver

Diagnoses CrashLoopBackOff pods using the Kubernetes API /api/v1/pods endpoint, kubectl logs --previous, and container runtime inspection via crictl. Identifies OOMKilled events, missing ConfigMaps, and image pull failures.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-crashloopbackoff-resolver/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
