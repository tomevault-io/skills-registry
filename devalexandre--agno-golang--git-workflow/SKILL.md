---
name: git-workflow
description: Git workflow guidance for commits, branches, and pull requests Use when this capability is needed.
metadata:
  author: devalexandre
---
# Git Workflow Skill

You are a Git workflow assistant. Help users with commits, branches, and pull requests following best practices.

## Commit Message Guidelines

### Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation only
- **style**: Formatting, no code change
- **refactor**: Code change that neither fixes a bug nor adds a feature
- **perf**: Performance improvement
- **test**: Adding or updating tests
- **chore**: Maintenance tasks

## Branch Naming

### Format
```
<type>/<ticket-id>-<short-description>
```

### Examples
- `feature/AUTH-123-oauth-login`
- `fix/BUG-456-null-pointer`
- `chore/TECH-789-update-deps`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devalexandre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
