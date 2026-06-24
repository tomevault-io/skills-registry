---
name: nobody-finishes-a-development-branch
description: Use when implementation is complete and all tests pass — guides completion by presenting structured options for merge, PR, or cleanup
metadata:
  author: hashwarlock
---

# Finishing a Development Branch

## Overview

When work is complete on a branch, follow a structured process to decide how to integrate and clean up.

**Announce at start:** "I'm using the nobody-finishes-a-development-branch skill to wrap this up."

## Pre-Completion Checklist

Before offering integration options:

- [ ] All tests pass (run them fresh)
- [ ] No uncommitted changes
- [ ] All plan tasks marked complete
- [ ] Code review passed (or self-reviewed with checklist)
- [ ] No debug code, TODOs, or temporary hacks remaining

## Integration Options

Present these options to the user:

### Option 1: Merge to main
```bash
git checkout main
git merge --no-ff feature/branch-name
git branch -d feature/branch-name
```
Best for: solo work, small changes, when you are the reviewer.

### Option 2: Create Pull Request
```bash
git push origin feature/branch-name
# Then create PR via GitHub/GitLab
```
Best for: team work, significant changes, when review is needed.

### Option 3: Keep branch (not done yet)
Continue working. Note what remains.

### Option 4: Discard
```bash
git checkout main
git branch -D feature/branch-name
```
For: abandoned experiments, wrong approach.

## Worktree Cleanup

If using git worktrees:

```bash
# Return to main worktree
cd /path/to/main

# Remove the worktree
git worktree remove .worktrees/branch-name

# Verify
git worktree list
```

## Summary Report

After integration, provide:

```
## Completed
- What was built
- Files changed: [count]
- Tests: [pass count]

## Key Decisions
- Notable design choices

## Follow-up
- Any remaining work or future improvements
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
