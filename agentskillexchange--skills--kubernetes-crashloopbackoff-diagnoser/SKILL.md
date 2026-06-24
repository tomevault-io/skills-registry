---
name: kubernetes-crashloopbackoff-diagnoser
description: Diagnoses CrashLoopBackOff pods using kubectl and the Kubernetes API. Inspects container logs, exit codes, OOMKilled events, and liveness probe configurations to generate actionable remediation steps. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Kubernetes CrashLoopBackOff Diagnoser

Diagnoses CrashLoopBackOff pods using kubectl and the Kubernetes API. Inspects container logs, exit codes, OOMKilled events, and liveness probe configurations to generate actionable remediation steps.

## Prerequisites

kubectl, Kubernetes API

## Installation

Use the upstream install or setup path that matches your environment:
- Make sure not to confuse Status , a kubectl display field for user intuition, with the pod's phase .

Requirements and caveats from upstream:
- Validate node setup
- Node-specific Volume Limits
- Node-pressure Eviction

Basic usage or getting-started notes:
- Learning environment
- Production environment
- Container Runtimes

- Source: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/

## Documentation

- https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/k8s-crashloopbackoff-diagnoser/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
