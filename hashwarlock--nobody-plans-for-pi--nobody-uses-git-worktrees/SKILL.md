---
name: nobody-uses-git-worktrees
description: Use when starting feature work that needs isolation from current workspace or before executing implementation plans
metadata:
  author: hashwarlock
---

# Using Git Worktrees

## Overview

Git worktrees create isolated workspaces sharing the same repository. Work on multiple branches simultaneously without switching.

**Announce at start:** "I'm using the nobody-uses-git-worktrees skill to set up an isolated workspace."

## Directory Selection

Check in priority order:

1. **Existing directory** — `ls -d .worktrees worktrees 2>/dev/null`
2. **Project config** — check AGENTS.md or project docs for preference
3. **Ask user** — ".worktrees/ (hidden, project-local) or global location?"

## Safety Verification

For project-local directories, verify they're gitignored:

```bash
git check-ignore -q .worktrees 2>/dev/null
```

If NOT ignored: add to `.gitignore` and commit before proceeding.

## Creation Steps

```bash
# 1. Create worktree with new branch
git worktree add .worktrees/feature-name -b feature/feature-name

# 2. Enter workspace
cd .worktrees/feature-name

# 3. Run project setup (auto-detect)
[ -f package.json ] && npm install
[ -f requirements.txt ] && pip install -r requirements.txt
[ -f Cargo.toml ] && cargo build

# 4. Verify clean baseline
# Run project-appropriate test command
```

## Report

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

If tests fail: report failures, ask whether to proceed.

## Cleanup

When done, use the nobody-finishes-a-development-branch skill, then:

```bash
git worktree remove .worktrees/feature-name
```

## Red Flags

- Creating worktree without verifying it's gitignored
- Skipping baseline test verification
- Proceeding with failing tests without asking
- Hardcoding setup commands instead of auto-detecting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
