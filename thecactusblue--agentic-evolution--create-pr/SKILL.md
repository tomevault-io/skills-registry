---
name: create-pr
description: Creates a pull request using the GitHub CLI.
metadata:
  author: thecactusblue
---

Steps:

1. Push the current branch to origin if not already pushed. If there are uncommitted changes, use /commit first.
2. Use `gh pr create` to create the pull request. Keep in mind that the base branch is named `dev`, not `main`.
3. Return the PR URL.

Write a concise PR title and description summarizing the changes. The description should include a comprehensive, yet concise description of everything that changed in the PR.

Do not describe every change verbatim: the diff viewer exists for that. Describe why, not how. DO NOT add an attribution footer.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thecactusblue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
