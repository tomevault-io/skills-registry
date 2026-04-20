---
name: tutordex-health-smoke
description: TutorDex health checks and smoke tests (backend health, worker/collector health, dependencies, readiness probes, observability checks). Use when this capability is needed.
metadata:
  author: darknubi
---

# TutorDex Health + Smoke

Use when asked "is prod healthy?", "did deploy succeed?", or before/after changes.

## Canonical checks

- `scripts/ops/smoke.sh --env staging|prod`

## Topology details

- Aggregator worker/collector health is exposed via backend routes:
  - `/health/worker`, `/health/dependencies`, `/health/collector`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darknubi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
