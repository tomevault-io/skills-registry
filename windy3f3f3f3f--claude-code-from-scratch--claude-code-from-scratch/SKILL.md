---
name: commit
description: Create a git commit with a summary of changes Use when this capability is needed.
metadata:
  author: Windy3f3f3f3f
---

# Commit Skill

1. Run `git diff --staged` and `git status` to see what's changed
2. Write a concise commit message summarizing the changes
3. Run `git commit -m "<message>"`

If no files are staged, tell the user to stage files first.
Arguments passed to this skill are used as additional context for the commit message.

---
> Source: [Windy3f3f3f3f/claude-code-from-scratch](https://github.com/Windy3f3f3f3f/claude-code-from-scratch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
