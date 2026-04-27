---
name: symfonydoctrine-batch-processing
description: Evolve Symfony Doctrine models and schema safely with integrity, performance, and rollout discipline. Use for doctrine batch processing tasks. Use when this capability is needed.
metadata:
  author: makfly
---

# Doctrine Batch Processing (Symfony)

## Use when
- Designing entity relations or schema evolution.
- Improving Doctrine correctness/performance.

## Default workflow
1. Model ownership/cardinality and transactional boundaries.
2. Apply mapping/schema changes with migration safety.
2. Tune fetch/query behavior for hot paths.
2. Verify lifecycle behavior with targeted tests.

## Guardrails
- Keep owning/inverse sides coherent.
- Avoid destructive migration jumps in one release.
- Eliminate accidental N+1 and over-fetching.

## Progressive disclosure
- Use this file for execution posture and risk controls.
- Open references when deep implementation details are needed.

## Output contract
- Entity/migration changes.
- Integrity and performance decisions.
- Validation outcomes and rollback notes.

## References
- `reference.md`
- `docs/complexity-tiers.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/makfly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
