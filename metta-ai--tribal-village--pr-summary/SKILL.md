---
name: pr-summary
description: Generate a PR title and description from the branch diff vs main. Use when asked for a PR summary or PR description. Use when this capability is needed.
metadata:
  author: metta-ai
---

# PR Summary

## Workflow
- Fetch origin if needed and compute merge base: `base=$(git merge-base HEAD origin/main)`.
- Review `git diff --stat $base` and key file diffs.
- Draft a PR title and short body that explain what changed and why.
- Include tests run or targeted test recommendations and any risks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
