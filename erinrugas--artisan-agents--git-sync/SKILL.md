---
name: git-sync
description: >- Use when this capability is needed.
metadata:
  author: erinrugas
---

# Git Sync Skill

## When to Apply
- User asks to sync with remote.
- Branch is behind base branch.
- Pre-PR branch cleanup is needed.

## Workflow
1. Fetch latest refs.
2. Choose strategy:
   - Fast-forward pull when possible.
   - Rebase for linear history.
   - Merge when preserving branch topology is required.
3. Resolve conflicts and re-validate locally.
4. Push safely (`--force-with-lease` only after rebase when necessary).

## Safety
- Never force-push protected branches.
- Prefer explicit base branch in commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erinrugas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
