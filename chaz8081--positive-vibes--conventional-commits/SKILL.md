---
name: conventional-commits
description: Enforces conventional commit message format for all git commits Use when this capability is needed.
metadata:
  author: chaz8081
---

# Conventional Commits

Always use the Conventional Commits format for all commit messages.

## Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Types

- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that do not affect the meaning of the code
- **refactor**: A code change that neither fixes a bug nor adds a feature
- **test**: Adding missing tests or correcting existing tests
- **chore**: Changes to the build process or auxiliary tools

## Rules

1. The commit message MUST start with a type
2. A scope MAY be provided after a type
3. A description MUST immediately follow the type/scope prefix
4. The description MUST be a short summary of the code changes
5. Breaking changes MUST be indicated by a `!` after the type/scope or by a `BREAKING CHANGE:` footer

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chaz8081) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
