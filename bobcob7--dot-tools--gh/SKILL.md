---
name: gh
description: GitHub CLI commands for PRs, issues, repos, and workflows Use when this capability is needed.
metadata:
  author: bobcob7
---

Help with GitHub CLI (`gh`) based on `$ARGUMENTS`.

## Authentication

```bash
gh auth login                 # Interactive login
gh auth status                # Check auth status
gh auth token                 # Print token
```

## Pull Requests

```bash
# Create
gh pr create                  # Interactive
gh pr create --title "Title" --body "Description"
gh pr create --draft          # Create as draft
gh pr create --base main      # Specify base branch

# View
gh pr list                    # List open PRs
gh pr view                    # View current branch's PR
gh pr view 123                # View PR #123
gh pr view --web              # Open in browser

# Review
gh pr checkout 123            # Check out PR branch
gh pr diff                    # View diff
gh pr review --approve        # Approve PR
gh pr review --comment -b "LGTM"
gh pr review --request-changes -b "Please fix..."

# Merge
gh pr merge                   # Merge current PR
gh pr merge --squash          # Squash merge
gh pr merge --rebase          # Rebase merge
gh pr merge --auto            # Auto-merge when ready
```

## Issues

```bash
gh issue create               # Interactive
gh issue create --title "Bug" --body "Details"
gh issue list                 # List open issues
gh issue view 123             # View issue
gh issue close 123            # Close issue
gh issue comment 123 --body "Comment"
```

## Repository

```bash
gh repo create                # Create new repo
gh repo clone owner/repo      # Clone repo
gh repo fork                  # Fork current repo
gh repo view                  # View repo info
gh repo view --web            # Open in browser
```

## Workflows (Actions)

```bash
gh workflow list              # List workflows
gh workflow run <name>        # Trigger workflow
gh run list                   # List runs
gh run view                   # View latest run
gh run watch                  # Watch run in progress
gh run rerun 12345            # Rerun failed run
```

## API

```bash
gh api repos/{owner}/{repo}   # GET request
gh api repos/{owner}/{repo}/issues --method POST -f title="New issue"
gh api graphql -f query='{ viewer { login } }'
```

## Aliases

```bash
gh alias set pv 'pr view'     # Create alias
gh alias list                 # List aliases
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobcob7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
