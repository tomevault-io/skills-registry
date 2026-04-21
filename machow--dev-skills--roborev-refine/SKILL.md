---
name: roborev-refine
description: Automated review-fix loop that keeps iterating until all code reviews pass. Use for hands-off code quality improvement. Use when this capability is needed.
metadata:
  author: machow
---

# Roborev Refine Skill

Automatically fix code review findings in a loop until everything passes.

## What it does

1. Finds failed reviews on the current branch
2. Runs an agent to fix findings
3. Commits the fixes
4. Waits for re-review
5. If still failing, repeats (up to 10 iterations by default)
6. Once per-commit reviews pass, runs a branch-level review
7. Addresses any branch-level findings too

The agent runs in an isolated worktree - your working directory stays clean.

## Prerequisites

- Must be in a git repository
- Working tree must be clean (commit or stash changes first)
- Must be on a feature branch (or use `--since` on main)

## Usage

Run the refine command:

```bash
roborev refine
```

Or with options:
- `roborev refine --since HEAD~5` - Only refine last 5 commits
- `roborev refine --max-iterations 5` - Limit retry attempts
- `roborev refine --fast` - Use faster (less thorough) reasoning

## Instructions

1. Check if the working tree is clean: `git status --porcelain`
   - If not clean, inform user they need to commit or stash first

2. Run: `roborev refine $ARGUMENTS`

3. Stream the output to the user - this can take a while as it iterates

4. Report the final result:
   - How many iterations it took
   - Whether all reviews now pass
   - Summary of changes made

## Related Skills

- `/roborev-review` - Run a single review (no auto-fix)
- `/roborev-address` - Manually address specific findings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/machow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
