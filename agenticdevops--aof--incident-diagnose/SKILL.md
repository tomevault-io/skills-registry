---
name: incident-diagnose
description: Multi-source incident analysis and diagnostics Use when this capability is needed.
metadata:
  author: agenticdevops
---

# Incident Diagnosis Skill

Systematically diagnose incidents by collecting data from multiple sources (K8s, metrics, logs).

## When to Use This Skill

- Responding to alerts
- Diagnosing service degradation
- Collecting incident context
- Understanding root cause
- Escalating with full context

## Steps

1. **Collect K8s state** — Get pods, events, resources
2. **Check metrics** — Query Prometheus for trends
3. **Review logs** — Search Loki for errors
4. **Correlate data** — Find patterns across sources
5. **Identify root cause** — Match patterns to known issues
6. **Suggest remediation** — Recommend actions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
