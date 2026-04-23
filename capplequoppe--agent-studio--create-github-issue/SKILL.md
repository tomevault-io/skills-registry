---
name: create-github-issue
description: Create an issue in GitHub Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Steps

1. Analyze the description in `$ARGUMENTS[0]` to understand the issue to be created.
2. Interview me about any necessary details regarding the issue.
3. Interview me to understand if the issue should be assigned to a milestone.
4. Determine the repository from `git remote get-url origin` (extract `{owner}/{repo}`).
5. Create a well-structured GitHub issue using the `gh` CLI:
   ```
   gh issue create --repo {owner}/{repo} --title "..." --body "..." [--milestone "..."] [--label "..."]
   ```
6. If a milestone was specified and it doesn't exist, create it first:
   ```
   gh api repos/{owner}/{repo}/milestones --method POST -f title="..." -f description="..."
   ```
7. Report the created issue URL.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
