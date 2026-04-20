---
name: rollback-strategy
description: | Use when this capability is needed.
metadata:
  author: alexgim961101
---

# Rollback Strategy Design

## Goal
Include a safe rollback plan in every design.

## Instructions

### Rollback Design Checklist
1. Is the DB migration backward-compatible?
2. Can the feature be disabled via feature flag?
3. Can Blue-Green / Canary deployment strategy be applied?
4. Data recovery scenario (backup point, restore procedure)
5. Dependent service rollback order

### Rollback Approach by Risk Level
- **Low**: Feature flag OFF
- **Medium**: Redeploy previous version + DB forward-compatible
- **High**: Full rollback + data restore + dependent service synchronized rollback

## Constraints
- Designs that cannot be rolled back must always include a prior warning
- Migrations with potential data loss must always include a backup strategy

## References
See `references/rollback-patterns.md` for additional rollback patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexgim961101) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
