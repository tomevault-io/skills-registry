---
name: merge-pr
description: Merge a GitHub PR via squash after /preparepr. Ensure PR ends in MERGED state and clean up worktrees after success. Use when this capability is needed.
metadata:
  author: carlooss-pm
---

# Merge PR

## Overview
Merge a prepared PR via `gh pr merge --squash` and clean up the worktree after success.

## Safety
- Use `gh pr merge --squash` as the only path to `main`.
- Do not run `git push` at all during merge.

## Steps

1. Identify PR meta
2. Run sanity checks (not draft, checks passing, not behind main)
3. Merge PR: `gh pr merge <PR> --squash --delete-branch`
   - If checks still running: use `--auto` to queue
4. Get merge SHA
5. Comment on PR thanking contributor
6. Verify PR state is MERGED (never CLOSED)
7. Clean up worktree only on success

## Guardrails
- Worktree only.
- Do not close PRs.
- End in MERGED state.
- Clean up only after merge success.
- Never push to main. Use `gh pr merge --squash` only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlooss-pm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
