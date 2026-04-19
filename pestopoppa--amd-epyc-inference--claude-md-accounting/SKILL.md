---
name: claude-md-accounting
description: Maintain governance accounting for all repository-relevant `CLAUDE.md` files and keep matrix documentation in sync. Use when adding/updating CLAUDE policy files, changing governance scope, or auditing which CLAUDE files are in vs out of migration scope. Use when this capability is needed.
metadata:
  author: pestopoppa
---

# CLAUDE.md Accounting

Use this skill when modifying CLAUDE policy surfaces.

Use when:

- Adding or updating repo-owned `CLAUDE.md` files.
- Auditing which CLAUDE files are governed vs excluded.

Do not use when:

- Editing third-party plugin/cache/backups for non-governance tasks.
- Performing unrelated role prompt wording tweaks.

## Workflow

1. Discover `CLAUDE.md` files.
2. Classify each path per `references/scoping.md`.
3. Update matrix docs and JSON.
4. Run `scripts/build_claude_md_matrix.py --check`.

## Governed Baseline

- `CLAUDE.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pestopoppa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
