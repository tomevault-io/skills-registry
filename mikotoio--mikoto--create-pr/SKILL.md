---
name: create-pr
description: Creates a pull request using the GitHub CLI.
metadata:
  author: mikotoio
---

Steps:

1. Push the current branch to origin if not already pushed. If there are uncommitted changes, use /commit first.
2. Use `gh pr create` to create the pull request.
3. Return the PR URL.

Write a concise PR title and description summarizing the changes. The description should include a comprehensive, yet concise description of everything that changed in the PR.

Do not describe every change verbatim: the diff viewer exists for that. Describe why, not how.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikotoio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
