---
name: prepare-pr
description: Prepare a GitHub PR for merge by rebasing onto main, fixing review findings, running gates, committing fixes, and pushing to the PR head branch. Use after /reviewpr. Never merge or push to main. Use when this capability is needed.
metadata:
  author: carlooss-pm
---

# Prepare PR

## Overview
Prepare a PR branch for merge with review fixes, green gates, and an updated head branch.

## Safety
- Never push to `main` or `origin/main`. Push only to the PR head branch.
- Never run bare `git push` without specifying remote and branch.
- Do not run `git clean -fdx` or `git add -A` or `git add .`.

## Completion Criteria
- Rebase PR commits onto `origin/main`.
- Fix all BLOCKER and IMPORTANT items from `.local/review.md`.
- Run required gates and pass.
- Commit prep changes.
- Push updated HEAD back to the PR head branch.
- Write `.local/prep.md` with prep summary.
- Output: `PR is ready for /mergepr`.

## Steps
1. Identify PR meta (author, head branch, head repo URL)
2. Fetch PR branch tip into local ref
3. Rebase PR commits onto latest main
4. Fix issues from `.local/review.md`
5. Update CHANGELOG.md if flagged
6. Update docs if flagged
7. Commit prep fixes (stage specific files only)
8. Run required gates: `pnpm install && pnpm build && pnpm ui:build && pnpm check && pnpm test`
9. Push to PR head branch with `--force-with-lease`
10. Verify PR is not behind main
11. Write `.local/prep.md`

## Guardrails
- Worktree only. Do not delete on success.
- Do not run `gh pr merge`. Never push to main.
- Max 3 fix-and-rerun cycles for gates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlooss-pm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
