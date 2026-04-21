---
name: review-data-release
description: Review migration/data-change safety and produce deployment go/no-go guidance. Use when this capability is needed.
metadata:
  author: abpaul
---

# Review Data Release

Use this skill before shipping migrations, backfills, or schema/data-shape changes.

## Checklist

- Migration safety and rollback strategy
- Data mapping integrity and orphan risk
- Backfill safety and idempotence
- Release sequencing and post-deploy verification queries

## Rails Touchpoints

- Validate migration reversibility and lock risk (table size/index impact).
- Confirm model/schema compatibility during rolling deploys.
- Verify background backfills are idempotent and retry-safe.
- Ensure strong params and serializers align with schema changes.
- Provide concrete post-deploy checks (`db:migrate:status`, verification queries).

## Context Discipline

- Start from changed migrations/models/jobs and only expand if coupled systems are affected.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
