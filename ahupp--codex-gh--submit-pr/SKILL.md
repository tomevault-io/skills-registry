---
name: submit-pr
description: Create and submit a GitHub pull request from a fork to the upstream source repo. Use when asked to open or submit a PR for the current branch or recent changes. Use when this capability is needed.
metadata:
  author: ahupp
---

# Submit PR

## Overview

Open a PR from the current fork branch to the upstream repository using `gh`, ensuring the branch is pushed and the base branch is correct.

## Workflow

1. Confirm the working tree is clean and commits exist. If there are uncommitted changes, run the commit workflow first.
2. Identify the upstream repo and default branch:
   - `gh repo view --json parent -q .parent.nameWithOwner` (must be non-null for forks)
   - `gh repo view <upstream> --json defaultBranchRef -q .defaultBranchRef.name`
3. Ensure an `upstream` remote exists; add if missing:
   - `git remote add upstream https://github.com/<upstream>.git`
4. Push the current branch to the fork:
   - `git push -u origin <branch>`
5. Create the PR targeting the upstream repo:
   - `gh pr create --repo <upstream> --base <default-branch> --head <fork-owner>:<branch> --title "<subject>" --body "<body>"`
6. Confirm the PR URL with `gh pr view --repo <upstream> --json url -q .url` and report it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahupp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
