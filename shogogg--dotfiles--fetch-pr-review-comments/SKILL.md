---
name: fetch-pr-review-comments
description: Fetches unresolved review comments from the GitHub PR for the current branch. Use when checking PR comments, addressing reviewer feedback, fixing review issues, or responding to code review. Use when this capability is needed.
metadata:
  author: shogogg
---

# Fetch PR Review Comments

Fetches unresolved review comments from the GitHub pull request corresponding to the current branch.
You should use the bundled script to do this.

## Instructions

Run the script: `bash ~/.claude/skills/fetch-pr-review-comments/get_unresolved_review_comments.sh`

## Notes
-
- Requires `gh` CLI to be authenticated
- Only works when on a branch that has an associated pull request

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shogogg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
