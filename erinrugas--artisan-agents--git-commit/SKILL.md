---
name: git-commit
description: >- Use when this capability is needed.
metadata:
  author: erinrugas
---

# Git Commit Skill

## When to Apply
- User asks to commit current changes.
- Commit message quality/history cleanup is needed.

## Workflow
1. Review changed files and group by intent.
2. Stage only relevant files (avoid accidental unrelated changes).
3. Write commit message using project convention (or Conventional Commits by default).
4. Verify commit contains only intended changes.

## Default Message Format
- `<type>(<scope>): <summary>`
- Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `ci`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erinrugas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
