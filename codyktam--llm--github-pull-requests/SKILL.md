---
name: pull-requests
description: Access pull requests (PRs) on GitHub using the GitHub CLI using approved, whitelisted commands. Use when this capability is needed.
metadata:
  author: codyktam
---

# GitHub Pull Request Workflow #
This is the GitHub CLI commands whitelist
If the command is not listed here, DO NOT RUN IT and let the user know the command you wanted to run, but are not allowed to.
User may provide a pull request number or a linear ticket number that is in the branch name.

## View my pull requests ##
Run `gh pr status` which returns `#PULL_REQUEST_NUMBER PULL_REQUEST_NAME [BRANCH_NAME]`

## View Pull Requests Requesting A Review From Me ##
Run `gh pr status`

## View pull request comments on current branch ##
Run `gh pr view`

## View changes in a pull request ## 
Run `gh pr diff`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyktam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
