---
name: fix-todos
description: Use when working with a skill to identify and fix special "TODO-AI" comments in the codebase. Use this when the user asks you to review AI comments, or mentions "TODO-AI" comments, or when you encounter "TODO-AI" comments in the code during your work.
metadata:
  author: emertechie
---

# Fix AI TODOs

1. Grep for the exact "TODO-AI" string in comments in the codebase to find all comments that need attention. DO NOT just search for "TODO" as that will return many unrelated comments.

2. For each "TODO-AI" comment, analyze the context and determine what action is needed to resolve it. If the asks you to discuss it, first explain your proposed fix and ask for confirmation before making any changes. If the user approves, implement the fix directly in the codebase.

3. After fixing each "TODO-AI", remove the "TODO-AI" comment from the code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/emertechie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
