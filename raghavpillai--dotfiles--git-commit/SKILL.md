---
name: git-commit
description: Use when working with the user's workflow to commit and push files properly
metadata:
  author: raghavpillai
---

The user has requested a git commit. You should NOT manually `git commit`, use this workflow.

First, use git status. Commits should be grouped into relevant groups of files that make sense to commit together. For each group:
- `git add <files>`: add this group of files
- `gencommit`: automatically generates a message and runs `git commit -m <generated_message>`

Then after all groups are committed, run `git push`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raghavpillai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
