---
name: commit-guidelines
description: Guidelines for creating git commits. Use when making commits, staging changes for commit, or deciding how to split changes across commits. Use when this capability is needed.
metadata:
  author: fmal
---

# Commit Guidelines

## Principles

- Each commit should represent a complete, working change
- Commit messages must use conventional commits format
- Keep the summary under 72 characters
- Explain what and why, not how

## Conventional Commits

Format: `<type>: <summary>`

Types:

| Type       | Purpose                                 |
| ---------- | --------------------------------------- |
| `feat`     | New feature                             |
| `fix`      | Bug fix                                 |
| `chore`    | Maintenance tasks, dependencies, config |
| `refactor` | Refactoring (no behavior change)        |
| `perf`     | Performance improvement                 |
| `docs`     | Documentation only                      |
| `test`     | Test additions or corrections           |
| `style`    | Code formatting (no logic change)       |

## Breaking Changes

Append `!` after the type and add a `BREAKING CHANGE:` footer:

```
feat!: remove deprecated v1 endpoints

BREAKING CHANGE: v1 endpoints no longer available
```

## Issue References

Reference GitHub issues in the commit footer:

- `Fixes #123` — closes the issue when merged
- `Refs #123` — links without closing

## Examples

```
feat: add OAuth2 login flow
```

```
fix: handle null response in user endpoint

The API could return null for deleted accounts, causing a crash.

Fixes #42
```

```
refactor: extract validation logic to shared module
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fmal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
