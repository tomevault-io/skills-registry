---
name: git-workflow
description: Git workflow best practices for commits, rebasing, conflict resolution, and branch management. Use when working with git operations, creating commits, resolving conflicts, or managing branches. Use when this capability is needed.
metadata:
  author: motlin
---

# Git Workflow Best Practices

This skill provides guidelines for git operations including commits, conflict resolution, and branch management.

## Commit Guidelines

**ALWAYS** delegate to the `git:commit-handler` agent for all commit operations. Never run `git commit` directly.

## Conflict Resolution

**ALWAYS** delegate to the `git:conflict-resolver` agent to resolve any git merge or rebase conflicts.

## Rebasing

**ALWAYS** delegate to the `git:rebaser` agent to rebase the current branch on upstream.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/motlin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
