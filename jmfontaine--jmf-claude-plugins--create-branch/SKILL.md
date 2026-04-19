---
name: create-branch
description: Git branch naming guidelines. Use when creating, renaming, or checking out new branches. Use when this capability is needed.
metadata:
  author: jmfontaine
---

# Git Branch Naming Guidelines

Follow the Conventional Branch specification with these details:

## Prefixes

Except for `main`, all branch names must use one of:

- `feat/`: New features (e.g., `feat/add-login-page`)
- `fix/`: Bug fixes (e.g., `fix/header-bug`)
- `hotfix/`: Urgent fixes (e.g., `hotfix/security-patch`)
- `release/`: Release preparation (e.g., `release/v1.2.0`)
- `chore/`: Non-code tasks (e.g., `chore/update-dependencies`)

## Additional Rules

- Include ticket numbers when applicable (e.g., `feat/issue-123-new-login`)
- Dots allowed only for version numbers in `release/` branches
- Check existing branches for naming patterns before creating new ones
- Do NOT use `git -C <path>` when the current directory is already the repository root

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jmfontaine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
