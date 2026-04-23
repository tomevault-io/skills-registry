---
name: commit
description: This skill should be used when the user asks to "commit changes", "create a commit", "git commit", or wants to save their staged changes with a meaningful message. Use when this capability is needed.
metadata:
  author: ngnnah
---

# /commit

Create a git commit with a well-crafted message.

## Instructions

1. Run `git status` and `git diff --staged` to see changes
2. If nothing is staged, ask user what to stage or stage all with `git add -A`
3. Analyze the changes and create a commit message that:
   - Starts with a verb (Add, Fix, Update, Remove, Refactor)
   - Summarizes the "why" not just the "what"
   - Keeps first line under 72 characters
4. Commit with the message
5. Show the commit hash when done

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngnnah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
