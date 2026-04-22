---
name: kdbplus-kx-engineering
description: kdb+ KX Engineering workflows for quantitative research, implementation, and production controls. use when tasks involve columnar schema design, point-in-time joins, and query-latency control. Use when this capability is needed.
metadata:
  author: ghostof0days
---

# kdb+ KX Engineering
## objective
Execute kdb+ kx engineering work with reproducible research, explicit controls, and deployable outputs.

## workflow
1. define source contracts, schema versions, and freshness objectives.
2. ingest data with replay support and deterministic normalization.
3. validate keys, timestamps, and point-in-time join behavior.
4. monitor quality metrics continuously and quarantine degraded feeds.
5. publish only when lineage, ownership, and quality thresholds are satisfied.

## required diagnostics
- freshness, completeness, null-rate, and duplicate-rate trends.
- schema drift and breaking-change frequency across sources.
- point-in-time join integrity for features and labels.
- backfill and replay consistency versus canonical snapshots.
- point-in-time join leakage and key integrity
- partition skew impact on read latency

## risk controls
- enforce hard thresholds for freshness and data-quality metrics.
- enforce quarantine and fallback paths for corrupted feeds.
- enforce full lineage metadata before downstream release.

## outputs
- run `python scripts/kdbplus_kx_engineering_diagnostics.py input.csv --output diagnostics.json` and keep the json artifact.
- write an implementation memo using `references/kdbplus-kx-engineering-playbook.md` with assumptions, tests, limits, and rollout plan.

## resources
- use `scripts/kdbplus_kx_engineering_diagnostics.py` for deterministic diagnostics.
- use `references/kdbplus-kx-engineering-playbook.md` for the domain-specific checklist and delivery structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ghostof0days) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
