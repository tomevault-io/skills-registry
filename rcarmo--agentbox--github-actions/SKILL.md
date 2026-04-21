---
name: github-actions-ci-patterns
description: CI patterns that call Make targets Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: GitHub Actions CI patterns

## Goal
Keep CI simple and repo-driven: workflows should call Make targets.

## Conventions
- Always include `actions/checkout@v4`.
- Prefer a single `make check` step.
- Keep language/tool setup minimal unless required.

## Files
- `.github/workflows/ci.yml`
- `.github/workflows/cleanup.yml`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
