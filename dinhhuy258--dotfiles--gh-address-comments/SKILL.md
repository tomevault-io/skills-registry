---
name: gh-address-comments
description: Help address review/issue comments on the open GitHub PR for the current branch using gh CLI Use when this capability is needed.
metadata:
  author: dinhhuy258
---

# PR Comment Handler

Guide to find the open PR for the current branch and address its comments with gh CLI.

## 1) Checkout the PR branch
- Run `gh pr checkout <pr>` to switch to the branch associated with the pull request

## 2) Inspect comments needing attention
- Run `<path-to-skill>/scripts/fetch_comments.py` which will print out all the comments and review threads on the PR

## 3) Ask the user for clarification
- Number all the review threads and comments and provide a short summary of what would be required to apply a fix for it
- Ask the user which numbered comments should be addressed

## 4) If user chooses comments
- Apply fixes for the selected comments

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dinhhuy258) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
