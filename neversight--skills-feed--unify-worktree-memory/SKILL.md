---
name: unify-worktree-memory
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# unify-worktree-memory

Open `@references/guide.md` and follow it. Do not proceed without it.

Consolidate Claude Code's per-project memory across git worktrees so every branch of the same repo shares accumulated knowledge. The solution uses symlinks (Claude Code follows them transparently) and a SessionStart hook for automatic setup of future worktrees.

The guide contains:
- How Claude Code memory paths work and why worktrees fragment them
- One-time consolidation script for existing worktrees
- SessionStart hook for automatic future worktree handling
- Path encoding rules and prefix collision disambiguation
- Edge cases: orphaned worktrees, ambiguous repo name prefixes, content merging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
