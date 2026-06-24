---
name: kubernetes-event-stream-analyzer
description: Watches Kubernetes event streams via the Watch API and correlates pod lifecycle events with resource metrics from Metrics Server. Detects CrashLoopBackOff patterns and OOMKilled signals for automated triage. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Event Stream Analyzer

Watches Kubernetes event streams via the Watch API and correlates pod lifecycle events with resource metrics from Metrics Server. Detects CrashLoopBackOff patterns and OOMKilled signals for automated triage.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-event-stream-analyzer/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
