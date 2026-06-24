---
name: simple-commit
description: Draft and create a conventional commit based on the current git diff. Use when this capability is needed.
metadata:
  author: por
---
1. First, run 'git diff' to see all changes (both staged and unstaged)
2. Analyze the diff to understand what changed
3. Write a conventional commit message based on the diff:
  - Use format: 'type(scope): description'
  - Types: feat, fix, docs, style, refactor, test, chore
  - Keep the first line under 72 characters
  - Add a blank line and bullet points for details if needed
  - Never mention any specific assistant or product
4. Stage all changes with 'git add -A'
5. Commit with the conventional commit message

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/por) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
