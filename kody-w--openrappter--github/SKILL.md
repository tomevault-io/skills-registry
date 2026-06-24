---
name: github
description: Interact with GitHub repositories, issues, pull requests, and actions using the gh CLI. Use when this capability is needed.
metadata:
  author: kody-w
---

# GitHub

Manage GitHub resources with the `gh` CLI.

## Issues

```bash
# List open issues
gh issue list

# Create an issue
gh issue create --title "Bug report" --body "Description"

# View an issue
gh issue view 123
```

## Pull Requests

```bash
# List PRs
gh pr list

# Create a PR
gh pr create --title "Feature" --body "Description"

# Review a PR
gh pr diff 456
gh pr review 456 --approve
```

## Actions

```bash
# List workflow runs
gh run list

# View a specific run
gh run view 789

# Re-run a failed workflow
gh run rerun 789
```

## API Access

```bash
# Query the GraphQL API
gh api graphql -f query='{ viewer { login } }'

# REST API
gh api repos/owner/repo/releases
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
