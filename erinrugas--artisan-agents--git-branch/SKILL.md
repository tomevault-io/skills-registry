---
name: git-branch
description: >- Use when this capability is needed.
metadata:
  author: erinrugas
---

# Git Branch Skill

## When to Apply
- User asks to create/switch/delete branches.
- Work needs branch hygiene before implementation.

## Workflow
1. Determine base branch (`main`, `develop`, or project default).
2. Create branch with consistent naming:
   - `feature/<topic>`
   - `fix/<topic>`
   - `chore/<topic>`
3. Verify upstream tracking after push.
4. Clean up merged branches when requested.

## Safety
- Do not delete unmerged branches without explicit confirmation.
- Avoid force operations unless necessary and approved.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erinrugas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
