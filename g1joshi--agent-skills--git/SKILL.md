---
name: git
description: Git version control with branching, merging, and rebasing. Use for source control. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Git

Git is the foundation of modern software. In 2025, features like **Sparse Checkout** and **Scalar** (for monorepos) are becoming mainstream.

## When to Use

- **Always**: Use it for everything. Text files, config, code.
- **Bisect**: Finding bugs by binary search.

## Core Concepts

### Graph

Commits form a DAG (Directed Acyclic Graph).

### Rebase vs Merge

- **Merge**: Preserves history, creates bubbles.
- **Rebase**: Rewrites history, linearizes.

### Worktrees

Checkout multiple branches of the same repo in different folders simultaneously.

## Best Practices (2025)

**Do**:

- **Use `git switch` / `git restore`**: The modern alternatives to the overloaded `git checkout`.
- **Use `git maintenance`**: Speed up fetch/clone in the background.
- **Sign Commits**: Use SSH keys to sign commits (`Commit Signing`).

**Don't**:

- **Don't force push to shared branches**: `git push --force-with-lease` is the safer alternative.

## References

- [Git Documentation](https://git-scm.com/doc)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
