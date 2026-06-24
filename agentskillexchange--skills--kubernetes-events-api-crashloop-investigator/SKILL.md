---
name: kubernetes-events-api-crashloop-investigator
description: Diagnoses restart storms with the Kubernetes Events API, Pod status conditions, and the Metrics API to explain why workloads are stuck in CrashLoopBackOff. Great for agents that need to summarize cluster evidence before an operator starts digging through kubectl output by hand. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes Events API CrashLoop Investigator

Diagnoses restart storms with the Kubernetes Events API, Pod status conditions, and the Metrics API to explain why workloads are stuck in CrashLoopBackOff. Great for agents that need to summarize cluster evidence before an operator starts digging through kubectl output by hand.

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

- [Agent Skill Exchange](https://agentskillexchange.com/skills/kubernetes-events-api-crashloop-investigator/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
