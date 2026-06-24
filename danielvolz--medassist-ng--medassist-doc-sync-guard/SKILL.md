---
name: medassist-doc-sync-guard
description: Ensure MedAssist documentation stays aligned with behavior changes in APIs, configuration, setup, and operations, including equivalent requests phrased in German. Use when this capability is needed.
metadata:
  author: danielvolz
---

# Skill Instructions

Use this skill when code changes alter behavior, setup steps, environment variables, user workflows, or operational commands.

## Objective

Keep docs consistent with actual product behavior and avoid stale setup/run guidance.

## Required Checks

1. If API behavior changed, verify relevant docs are updated.
2. If ENV/config changed, update documented variables/defaults.
3. If workflow/commands changed, update setup/run instructions.
4. If user-facing behavior changed, update user-facing description.

## Candidate Documentation Files

- `README.md`
- `docs/PROJECT_SETUP.md`
- `docs/TECH_STACK.md`

## Anti-Patterns

- Shipping behavior changes without docs updates.
- Updating docs with speculative/unverified commands.
- Duplicating conflicting instructions across files.

## Response Format

Return:

- Doc files that should change
- Proposed update summary per file
- Any intentionally skipped docs and reason

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielvolz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
