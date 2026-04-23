---
name: create-github-pr
description: Create a pull request in GitHub Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Steps

1. Ensure that the current working branch has been pushed to the remote repository:
   ```
   git push -u origin HEAD
   ```
2. Ensure that the main branch is up to date with the remote:
   ```
   git fetch origin main
   ```
3. Ensure that the main branch has been merged into the current working branch.
4. Check the git diffs between the current working branch and the main branch:
   ```
   git diff origin/main...HEAD
   ```
5. Formulate a summary of the changes made, clearly describing the purpose and impact.
6. Create the pull request using the `gh` CLI:
   ```
   gh pr create --title "..." --body "..." [--base main]
   ```
   The `$ARGUMENTS[0]` argument, if provided, is used as additional context for the PR title/description.
7. Report the created PR URL.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
