---
name: github
description: Interact with GitHub using the gh CLI for issues, PRs, and repository management. Use when this capability is needed.
metadata:
  author: owliabot
---

# GitHub CLI

Use `gh` for GitHub operations. Must be authenticated (`gh auth status`).

## Issues

```bash
# List open issues
gh issue list

# Create issue
gh issue create --title "Bug: ..." --body "Description"

# View issue
gh issue view 123

# Close issue
gh issue close 123
```

## Pull Requests

```bash
# List PRs
gh pr list

# Create PR
gh pr create --title "Feature: ..." --body "Description"

# View PR
gh pr view 123

# Merge PR
gh pr merge 123 --squash
```

## Repository

```bash
# Clone repo
gh repo clone owner/repo

# View repo info
gh repo view

# Create repo
gh repo create my-repo --public
```

## Workflow Runs

```bash
# List runs
gh run list

# View run details
gh run view 12345

# Watch run
gh run watch 12345
```

## API Access

For advanced queries:

```bash
gh api repos/{owner}/{repo}/issues --jq '.[].title'
```

## Tips

- Use `--json` for machine-readable output
- Use `--jq` to filter JSON results
- Set repo context: `gh repo set-default owner/repo`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/owliabot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
