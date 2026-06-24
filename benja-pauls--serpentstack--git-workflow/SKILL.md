---
name: git-workflow
description: Git conventions for this project. Use when: creating branches, writing commit messages, opening PRs, or running the pre-push checklist. Use when this capability is needed.
metadata:
  author: Benja-Pauls
---

# Git Workflow

## Branch Naming

Use prefixed branch names:

- `feature/short-description` — new functionality
- `fix/short-description` — bug fixes
- `chore/short-description` — tooling, dependencies, config
- `refactor/short-description` — code restructuring

Lowercase, hyphens, under 50 characters.

## Commit Messages

Follow Conventional Commits:

```
<type>: <short summary under 72 chars>
```

Types: `feat`, `fix`, `chore`, `docs`, `test`, `refactor`, `style`

## Pull Request Format

```bash
gh pr create \
  --title "feat: add projects endpoint" \
  --body "## Summary
- What changed and why (1-3 bullets)

## Test plan
- How the changes were verified"
```

## Pre-Push Checklist

Before pushing, run the full verification suite:

```bash
make verify   # lint + typecheck + test for both backend and frontend
```

This runs the same checks as CI, so if it passes locally, CI will pass too.

---
> Source: [Benja-Pauls/SerpentStack](https://github.com/Benja-Pauls/SerpentStack) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
