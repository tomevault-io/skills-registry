---
name: worktree-workflow
description: | Use when this capability is needed.
metadata:
  author: nathanchase
---

# Worktree Workflow

Manages git worktrees for isolated feature development. Creates worktrees before implementation, cleans up after merge.

## Current Worktree Status

!`bash .claude/skills/worktree-workflow/status.sh 2>/dev/null || git worktree list`

## Phase 1: Before Implementation

**CRITICAL**: Before writing ANY implementation code for an approved plan, create a worktree.

### 1. Derive Branch Name

From the plan context, create a descriptive branch name:
- `feature/<name>` - New functionality
- `fix/<name>` - Bug fixes
- `refactor/<name>` - Code restructuring
- `chore/<name>` - Maintenance tasks

Use kebab-case, keep it short but descriptive.

### 2. Create Branch and Worktree

```bash
# Ensure base branch is current
git fetch origin <base-branch>

# Create branch from base
git branch <branch-name> origin/<base-branch>

# Create worktree
git worktree add <project-root>-<short-name> <branch-name>

# Configure git identity in worktree
cd <project-root>-<short-name>
git config user.name "<Your Name>"
git config user.email "<your@email.com>"
```

### 3. Work in Worktree

All file operations during implementation use the worktree path, not the main repo.

## Phase 2: After Merge

When a branch's PR is merged:

### Check if Merged

```bash
gh pr view <branch-name> --json state,mergedAt
```

### Cleanup

```bash
git worktree remove <worktree-path>
git branch -D <branch-name>
```

## Important Rules

1. **Never implement in main repo** - Always use a worktree
2. **One worktree per feature** - Don't mix unrelated changes
3. **Clean up promptly** - Remove worktrees after PRs merge
4. **Protected branches** - Never delete your base branch (main, develop, master)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nathanchase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
