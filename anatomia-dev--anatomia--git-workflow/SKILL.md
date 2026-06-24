---
name: git-workflow
description: Invoke before any git operations — branching, committing, merging, or creating pull requests. Contains project-specific branch naming, commit format, and merge strategy. Use when this capability is needed.
metadata:
  author: anatomia-dev
---

# Git Workflow

## Detected
<!-- Populated by scan during init. Do not edit manually. -->

## Rules
- Commit each logical change separately. Don't batch unrelated changes into one commit.
- Write commit messages that explain what changed and why: `feat: add input validation to signup` not `update files`.
- Stage specific files for each commit. Avoid `git add .` or `git add -A` — review what you're committing.

## Gotchas
*Not yet captured. Add as you discover them during development.*

## Examples
*Not yet captured. Add short snippets showing the RIGHT way.*

---
> Source: [anatomia-dev/anatomia](https://github.com/anatomia-dev/anatomia) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
