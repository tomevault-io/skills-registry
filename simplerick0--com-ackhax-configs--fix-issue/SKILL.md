---
name: fix-issue
description: Fix a GitHub issue by analyzing, implementing, and creating a PR. Use when the user provides an issue number or asks to fix/resolve/address a GitHub issue. Use when this capability is needed.
metadata:
  author: simplerick0
---

# Fix Issue

Fix GitHub issue #$0:

1. Fetch issue: `gh issue view $0`
2. Create branch: `git checkout -b fix/issue-$0`
3. Implement fix following project patterns
4. Commit: `Fix #$0: [description]`
5. Create PR: `gh pr create --fill`

Reference the issue number in commit and PR to enable auto-linking.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/simplerick0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
