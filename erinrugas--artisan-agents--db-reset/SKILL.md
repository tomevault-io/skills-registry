---
name: db-reset
description: >- Use when this capability is needed.
metadata:
  author: erinrugas
---

# DB Reset Skill

## When to Apply
- User requests a clean data reset.
- Migration/seeding drift causes local breakage.
- Test data must be rebuilt from scratch.

## Workflow
1. Confirm environment scope (local/test/staging) and risk.
2. Identify reset command pattern from project tooling.
3. Execute reset + seed using project-standard commands.
4. Validate schema status and key baseline data.
5. Report exactly what was destroyed/recreated.

## Safety
- Do not run destructive reset on production data.
- Prefer scoped resets where supported.
- Call out irreversible actions before execution.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erinrugas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
