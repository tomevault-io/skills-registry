---
name: gh-cli
description: GitHub CLI (gh) comprehensive reference for repositories, issues, pull requests, Actions, projects, releases, gists, codespaces, organizations, extensions, and all GitHub operations from the command line. Use when this capability is needed.
metadata:
  author: valdarix
---

# GitHub CLI (gh)

Comprehensive reference for GitHub CLI (gh) — work seamlessly with GitHub from the command line.

**Version:** 2.85.0 (current as of January 2026)

## Prerequisites

### Installation

```bash
# macOS
brew install gh

# Linux
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh

# Windows
winget install --id GitHub.cli

# Verify installation
gh --version
```

### Authentication

```bash
# Interactive login (default: github.com)
gh auth login

# Login with token
gh auth login --with-token < mytoken.txt

# Check authentication status
gh auth status

# Configure git credential helper
gh auth setup-git
```

## Issues

### Create Issue

```bash
# Create issue interactively
gh issue create

# Create with title and body
gh issue create \
  --title "Bug: Login not working" \
  --body "Steps to reproduce..."

# Create with labels and assignees
gh issue create --title "Fix bug" --labels bug,high-priority --assignee user1,user2

# Create from file
gh issue create --body-file issue.md
```

### List / View Issues

```bash
# List open issues
gh issue list

# Filter by labels, assignee, state
gh issue list --labels bug --assignee @me --state all

# JSON output with jq
gh issue list --json number,title,state --jq '.[] | [.number, .title] | @tsv'

# View issue with comments
gh issue view 123 --comments
```

### Edit / Close Issues

```bash
# Edit issue
gh issue edit 123 --title "New title" --add-label enhancement

# Close with comment
gh issue close 123 --comment "Fixed in PR #456"

# Develop: create branch from issue
gh issue develop 123 --branch feat/issue-123
```

## Pull Requests

### Create PR

```bash
# Create PR interactively
gh pr create

# Create with details
gh pr create \
  --title "feat: add user registration" \
  --body "Closes #123" \
  --base main \
  --reviewer user1

# Create draft PR
gh pr create --draft

# Create linking to issue
gh pr create --title "Fix #123" --body "Closes #123"
```

### List / View PRs

```bash
# List open PRs
gh pr list

# Filter by author, labels, state
gh pr list --author @me --state merged --labels enhancement

# View PR with details
gh pr view 123 --comments

# View PR checks
gh pr checks 123 --watch
```

### Review PR

```bash
# Approve
gh pr review 123 --approve --body "LGTM!"

# Request changes
gh pr review 123 --request-changes --body "Please fix these issues"

# Comment
gh pr review 123 --comment --body "Some thoughts..."
```

### Merge / Close PR

```bash
# Merge with squash and delete branch
gh pr merge 123 --squash --delete-branch

# Close without merging
gh pr close 123 --comment "Superseded by #456"
```

### Checkout / Diff

```bash
# Checkout PR branch locally
gh pr checkout 123

# View PR diff
gh pr diff 123
```

## Repositories

```bash
# Create repo
gh repo create my-repo --public --description "My project" --clone

# Clone
gh repo clone owner/repo

# View
gh repo view --json name,description,defaultBranchRef

# Set default
gh repo set-default owner/repo
```

## GitHub Actions

```bash
# List workflow runs
gh run list --workflow "ci.yml" --branch main

# View run details and logs
gh run view 123456789 --log

# Watch run in real-time
gh run watch 123456789

# Rerun failed run
gh run rerun 123456789

# Trigger workflow
gh workflow run ci.yml --ref main
```

## Search

```bash
# Search code, issues, PRs, repos
gh search code "TODO" --repo owner/repo
gh search issues "label:bug state:open"
gh search prs "is:open review:required"
gh search repos "stars:>1000 language:python"
```

## Labels

```bash
# Create label
gh label create bug --color "d73a4a" --description "Something isn't working"

# List / edit / delete
gh label list
gh label edit bug --name "bug-report" --color "ff0000"
gh label delete stale
```

## API Requests

```bash
# REST API
gh api /user
gh api --method POST /repos/owner/repo/issues --field title="Issue" --field body="Body"

# GraphQL
gh api graphql -f query='{ viewer { login } }'

# With jq
gh api /repos/owner/repo --jq '.stargazers_count'
```

## Common Workflows

### Issue-Driven Development

```bash
# 1. Create issue for the task
gh issue create --title "feat: add search" --labels enhancement

# 2. Create branch from issue
gh issue develop 42 --branch feat/issue-42

# 3. Work on the feature, commit, push
git add . && git commit -m "feat: add search functionality

Closes #42"
git push -u origin feat/issue-42

# 4. Create PR linking to issue
gh pr create --title "feat: add search" --body "Closes #42"

# 5. Request review
gh pr edit 99 --add-reviewer code-reviewer

# 6. After approval, merge
gh pr merge 99 --squash --delete-branch
```

### Bulk Operations

```bash
# Close stale issues
gh issue list --search "label:stale" --json number --jq '.[].number' | \
  xargs -I {} gh issue close {} --comment "Closing as stale"

# Add label to multiple PRs
gh pr list --search "review:required" --json number --jq '.[].number' | \
  xargs -I {} gh pr edit {} --add-label needs-review
```

## Global Flags

| Flag | Description |
|------|-------------|
| `--repo [HOST/]OWNER/REPO` | Select another repository |
| `--jq EXPRESSION` | Filter JSON output |
| `--json FIELDS` | Output JSON with specified fields |
| `--web` | Open in browser |
| `--paginate` | Make additional API calls for full results |

## Best Practices

1. **Use `gh issue develop`** to create branches linked to issues automatically
2. **Link PRs to issues** with `Closes #N` in the PR body to auto-close on merge
3. **Use JSON output + jq** for scripting: `gh issue list --json number,title --jq '...'`
4. **Set default repo** to avoid `--repo` on every command: `gh repo set-default`
5. **Use `gh pr checks --watch`** to monitor CI status before merging

## References

- Official Manual: https://cli.github.com/manual/
- GitHub Docs: https://docs.github.com/en/github-cli

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valdarix) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
