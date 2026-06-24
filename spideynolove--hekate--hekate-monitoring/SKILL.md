---
name: hekate-monitoring
description: Inspect Hekate v2 monitoring outputs and transient coordination health Use when this capability is needed.
metadata:
  author: spideynolove
---

# Hekate Monitoring

## Dashboard

```bash
./scripts/hekate-dashboard.py
```

## Snapshot Analysis

```bash
./scripts/hekate-analyze.py
```

## Prometheus Export

```bash
./scripts/hekate-dashboard.py --prometheus
```

## Coordination Health

```bash
redis-cli keys "task:*:claim"
redis-cli keys "agent:*:heartbeat"
redis-cli keys "quota:*"
```

## Durable State

Use the dashboard and analysis scripts for SQLite-backed epic and task views. Redis output is only a live overlay.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spideynolove) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
