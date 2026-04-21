---
name: docs-freshness
description: Detect doc drift and require README/spec/changelog updates when code or protocol changes. Use when this capability is needed.
metadata:
  author: jmduea
---

# Skill: docs-freshness

## Purpose

Map source/protocol changes to required documentation updates.
This skill is executed under writer-owned docs freshness flow.

## Inputs

- Changed files in Rust/Python/docs trees.
- Public API/behavior/protocol deltas.

## Checks

1. Public behavior changed -> `CHANGELOG.md` reviewed.
2. Protocol changed -> protocol docs and examples updated.
3. New config/flags added -> README/spec/docs updated.
4. Notebook/tutorial paths still valid.

## Output

- Required doc updates list.
- Blocking doc gaps.
- Docs freshness verdict (pass/fail).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jmduea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
