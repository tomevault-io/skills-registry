---
name: gh-default-branch
description: Retrieve the default branch name of the current Git repository using GitHub CLI. Use when asked "What is the default branch?", "Get the default branch", "What is the main branch?", "Show me the base branch", "get repo default branch", or when git operations require the default branch name (creating PRs, comparing branches, checking out base branch). Returns branch names like main, master, or develop using GitHub CLI. Use when this capability is needed.
metadata:
  author: puku0x
---

# Get Default Branch

## Overview

This skill retrieves the default branch name of the current Git repository using the GitHub CLI command `gh repo view --json defaultBranchRef --jq .defaultBranchRef.name`.

## When to Use

Use this skill when:

- The user requests the default branch name of the repository.
- Performing git operations that require knowledge of the default branch (e.g., creating pull requests, comparing branches, checking out the base branch).

## Instructions

To get the default branch name, run the command:

```bash
gh repo view --json defaultBranchRef --jq .defaultBranchRef.name
```

The command will output the default branch name (e.g., `main`, `master`, `develop`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/puku0x) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
