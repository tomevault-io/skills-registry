---
name: managing-branches
description: > Use when this capability is needed.
metadata:
  author: 1gy
---

# Branch Investigation

```bash
git branch --show-current
git status --short
git fetch --all
git branch -vv
git rev-list --count <main-branch>..HEAD 2>/dev/null || echo "0"  # Check CLAUDE.md for main branch name
```

Report: current branch, uncommitted changes, remote sync status, commits ahead of main.

# Branch Creation

```bash
git fetch origin <base-branch>
git checkout -b <new-branch> origin/<base-branch>
```

# Error Handling

| Error | Action |
|-------|--------|
| Branch exists | Report to user, suggest alternative or confirm use existing |
| Uncommitted changes | `git stash` or commit first |
| Remote sync error | `git fetch --all` retry |
| Permission error | Report to user |

# Conflict Resolution

1. `git status` to identify conflicts
2. Resolve each file
3. `git add <resolved-file>`
4. Continue operation

Ask for guidance if resolution is complex.

# Completion Report

- Current branch name
- Branch creation result (if applicable)
- Any issues encountered

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1gy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
