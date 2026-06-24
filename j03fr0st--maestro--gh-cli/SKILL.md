---
name: gh-cli
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# GitHub CLI (gh)

Work with GitHub from the command line. See [full reference](references/REFERENCE.md) for complete documentation.

## Prerequisites

```bash
# Install
brew install gh              # macOS
winget install GitHub.cli    # Windows
sudo apt install gh          # Debian/Ubuntu (via https://github.com/cli/cli/blob/trunk/docs/install_linux.md)
sudo dnf install gh          # Fedora

# Authenticate (required before any gh commands work)
gh auth login

# Verify
gh auth status
```

## Quick Reference

### Pull Requests

```bash
# Create PR
gh pr create --title "feat: add feature" --body "Description"

# List PRs
gh pr list
gh pr list --author @me

# View/checkout PR
gh pr view 123
gh pr checkout 123

# Merge PR (--squash keeps history clean; --delete-branch avoids stale branches)
gh pr merge 123 --squash --delete-branch

# Review PR
gh pr review 123 --approve
gh pr review 123 --request-changes --body "Please fix..."
```

### Issues

```bash
# Create issue
gh issue create --title "Bug: description" --label bug

# List issues
gh issue list
gh issue list --assignee @me

# View/close issue
gh issue view 123
gh issue close 123 --comment "Fixed in PR #456"
```

### Repositories

```bash
# Clone
gh repo clone owner/repo

# Create
gh repo create my-repo --public --description "My project"

# View
gh repo view
gh repo view --web
```

### GitHub Actions

```bash
# List workflow runs
gh run list

# View run (--log shows full output, useful for debugging failures)
gh run view 123456789
gh run view 123456789 --log

# Watch run (blocks until complete, good for waiting on CI)
gh run watch 123456789

# Trigger workflow
gh workflow run ci.yml --ref main
```

### Releases

```bash
# Create release (uses tag as version identifier)
gh release create v1.0.0 --notes "Release notes"

# List releases
gh release list

# Download assets
gh release download v1.0.0
```

## Common Patterns

### Create PR from Current Branch

```bash
# --fill auto-populates title/body from commit messages, saving time on single-commit PRs
gh pr create --fill
gh pr create --title "feat: description" --body "Details"
```

### Check PR Status

```bash
# --watch polls until all checks complete
gh pr checks 123 --watch
```

### API Requests

```bash
# For anything not covered by built-in commands, use the API directly
gh api /user
gh api /repos/owner/repo/issues --method POST --field title="Issue"
```

### JSON Output

```bash
# --json + --jq is useful for scripting and extracting specific fields
gh pr list --json number,title,author
gh pr view 123 --json title,body,state --jq '.title'
```

## Global Flags

| Flag | Description |
|------|-------------|
| `--repo owner/repo` | Target a specific repository (when not in a git repo) |
| `--json FIELDS` | Output as JSON |
| `--jq EXPRESSION` | Filter JSON output |
| `--web` | Open in browser instead of terminal |

## Edge Cases

- Use `--repo` flag when not inside a git repository
- Use `gh auth refresh --scopes` to add permissions (e.g., for org-level operations)
- Use `--paginate` for large result sets that exceed default page size
- Draft PRs need `--draft` flag

## References

- [Full Command Reference](references/REFERENCE.md)
- [Official Manual](https://cli.github.com/manual/)
- [GitHub Docs](https://docs.github.com/en/github-cli)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
