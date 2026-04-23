---
name: k8s-logs
description: Kubernetes log retrieval and analysis Use when this capability is needed.
metadata:
  author: agenticdevops
---

# Kubernetes Logs Skill

Retrieve and analyze logs from Kubernetes pods to understand application behavior and troubleshoot issues.

## When to Use This Skill

- Need to view pod logs
- Searching for specific error messages
- Analyzing application behavior
- Debugging intercontainer communication
- Following log streams in real-time

## Steps

1. **Get recent logs** — `kubectl logs {pod-name} --tail=50`
2. **Get all logs** — `kubectl logs {pod-name} --all-containers=true`
3. **Search logs** — `kubectl logs {pod-name} | grep ERROR`
4. **Follow logs** — `kubectl logs {pod-name} -f`
5. **Get previous logs** — `kubectl logs {pod-name} --previous`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
