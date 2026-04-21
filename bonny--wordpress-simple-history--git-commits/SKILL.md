---
name: git-commits
description: Provides project-specific git commit guidance for Simple History, especially for changes spanning core and premium plugins. Triggers: "commit", "multi-repo commit".
metadata:
  author: bonny
---

# Git Commits - Project Specifics

Project-specific commit guidance for Simple History.

## Multi-Repository Workflow

When changes span core (this repo) and premium (simple-history-premium):

1. **Commit core first**, then premium
2. **Use related commit messages** that reference each other
3. **Push core before premium** when dependencies exist

## When to Split Commits

**Separate commits:**
- CSS vs PHP logic changes
- Different features (even in same file)
- Refactoring vs new functionality
- Multiple independent bug fixes

**Single commit:**
- Related changes for one feature
- A handler + its CSS
- Tests for the feature being added

## Commit Message Style

Follow existing repo style - check `git log --oneline -5` for examples.

```
<summary - what and why>

<optional body with context>
```

Keep summary concise and descriptive.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bonny) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
