---
name: github-merge-pr
description: Merges a pull request after verifying checks pass. Use when this capability is needed.
metadata:
  author: erikbeerepoot
---

# Merge Pull Request

## Instructions
1. If no PR number provided, find the PR for the current branch
2. Check PR status (reviews, CI checks)
3. Warn if checks are failing or reviews are pending
4. Merge the PR using squash merge by default
5. Optionally delete the branch after merge

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erikbeerepoot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
