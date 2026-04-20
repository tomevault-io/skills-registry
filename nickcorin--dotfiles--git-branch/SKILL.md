---
name: git-branch
description: Create consistently named git branches. Use when this capability is needed.
metadata:
  author: nickcorin
---

## What I do

- Create git branches.

## When to use me

Use this when you are preparing to create a new branch in a git repository.

## Branching rules

**Naming convention:** `<context>/[issue-number-]<short-description>`

Contexts:

- `chore`: non-code tasks like dependency bumps
- `doc`: documentation changes
- `feat`: new features
- `fix`: bug fixes
- `ref`: refactoring without functionality changes
- `rfc`: RFC design documents
- `release`: preparing a release.

Examples:

- `feat/1234-user-authentication`
- `fix/5678-memory-leak`
- `doc/improve-readme`

Requirements:

- Main branch SHALL be `master`
- Feature branches MUST NOT be long-lived
- All changes MUST go through pull requests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcorin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
