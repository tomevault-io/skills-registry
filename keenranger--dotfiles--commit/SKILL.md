---
name: commit
description: Prepare git commit with review. Use when user asks to commit, review changes for commit, or says /commit. Use when this capability is needed.
metadata:
  author: keenranger
---

Prepare commit: $ARGUMENTS

Review current changes and prepare commit for user approval.
Call git-diff-reviewer agent and toss process to it.

Process:

- Run git status and git diff to see all changes
- Review changes for quality and completeness
- Suggest commit message (use imperative form)

Output:

- Review summary of changes
- Suggested commit message
- Wait for user approval before executing commit

Goal: Thoughtful commit preparation without auto-committing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keenranger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
