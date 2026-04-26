---
name: git-workflow
description: Git workflow and pull request specialist. Use when creating PRs, managing Use when this capability is needed.
metadata:
  author: 89jobrien
---

# Git Workflow Skill

Expert guidance for git workflows, pull request best practices, branch management, and code review processes.

## What This Skill Does

- Creates well-structured pull requests
- Establishes branch naming conventions
- Defines PR templates and checklists
- Reviews PR quality
- Manages merge strategies
- Handles rebasing and conflict resolution

## When to Use

- Creating pull requests
- Establishing team git conventions
- Improving code review process
- Branch strategy decisions
- PR template creation

## Reference Files

- `references/PULL_REQUEST.template.md` - PR body templates for features, bugs, refactors

## Branch Naming

```
<type>/<issue-number>-<short-description>
```

Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

## PR Best Practices

1. Clear title following conventional format
2. Summary explaining WHAT and WHY
3. Specific test plan
4. Breaking changes documented
5. Screenshots for UI changes
6. Link to related issues

## Merge Strategies

| Strategy | When to Use |
|----------|-------------|
| Squash | Feature branches, clean history |
| Merge | Preserve commits, audit trail |
| Rebase | Linear history, small changes |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/89jobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
