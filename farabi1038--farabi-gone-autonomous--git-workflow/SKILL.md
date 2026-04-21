---
name: git-workflow
description: Git version control expert for branching, merging, and collaboration workflows Use when this capability is needed.
metadata:
  author: farabi1038
---

# Git Workflow

Expert in Git version control, branching strategies, and team collaboration workflows.

## Branching Strategies

### GitFlow
- `main` - production-ready code
- `develop` - integration branch
- `feature/*` - new features
- `release/*` - release preparation
- `hotfix/*` - production fixes

### Trunk-Based Development
- Short-lived feature branches
- Frequent merges to main
- Feature flags for incomplete work

## Common Operations

### Starting New Work
```bash
git checkout -b feature/my-feature
git push -u origin feature/my-feature
```

### Keeping Branch Updated
```bash
git fetch origin
git rebase origin/main
```

### Clean Commit History
```bash
git rebase -i HEAD~3  # Squash last 3 commits
```

## Commit Message Format

```
type(scope): subject

body

footer
```

Types: feat, fix, docs, style, refactor, test, chore

## Conflict Resolution

1. Identify conflicting files
2. Open files and look for conflict markers
3. Decide which changes to keep
4. Remove conflict markers
5. Stage and commit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/farabi1038) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
