---
name: commit
description: Create a well-structured git commit with conventional format Use when this capability is needed.
metadata:
  author: anotherjson
---

Create a git commit for the current changes.

Steps:
1. Run `git status` and `git diff --staged` (or `git diff` if nothing staged)
2. Analyze changes to determine type: feat, fix, refactor, docs, test, chore
3. Draft a conventional commit message: `type(scope): description`
4. Stage relevant files if needed (never stage .env, credentials, or secrets)
5. Show the proposed message and ask for confirmation
6. Commit with the approved message

$ARGUMENTS is an optional hint about what the commit is for.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anotherjson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
