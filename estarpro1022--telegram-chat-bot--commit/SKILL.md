---
name: commit
description: Create well-formatted git commits following conventional commit standards. Use this skill when the user asks to create a git commit, requests to commit changes, or wants to commit staged changes. Use when this capability is needed.
metadata:
  author: estarpro1022
---

# Git Commit Skill

Create well-formatted git commits following conventional commit standards.

## Usage
```
/commit
```

## Behavior
1. Analyze staged changes with `git diff --staged`
2. Generate a conventional commit message
3. Create the commit with proper formatting

## Commit Format
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

## Types
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- style: Code style changes
- refactor: Code refactoring
- test: Adding or modifying tests
- chore: Maintenance tasks

## Example Output
```
feat(auth): add password reset functionality

- Add forgot password form
- Implement email verification flow
- Add password reset endpoint
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/estarpro1022) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
