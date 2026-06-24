---
name: tools-github-workflow
description: Full GitHub workflow orchestration via CLI - branch management, commit quality, issue triage, PR lifecycle, and worktree operations on macOS and Windows. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# GitHub Workflow Orchestration

## Overview
Manage the complete GitHub development workflow from the terminal using `gh` and `git`. Covers branch hygiene, commit crafting, issue triage, PR lifecycle, and parallel worktree operations.

## Branch Management

### Sync & Create
```bash
# Sync with upstream
gh repo sync --force
git fetch origin main && git rebase origin/main

# Create feature branch
git switch -c feature/payments-123 origin/main

# Checkout existing PR
gh pr checkout <number>

# Set default repo
gh repo set-default org/repo
```

### Naming Convention
```
feature/<area>-<ticket>     # New features
bugfix/<ticket>             # Bug fixes
hotfix/<ticket>             # Production fixes
release/<version>           # Release branches
```

### Daily Hygiene
```bash
# Rebase on main
git fetch origin main && git rebase origin/main

# Push with tracking
git push -u origin feature/payments-123

# If conflicts: abort, fix, rerun
git rebase --abort
```

### Audit & Cleanup
```bash
# List remote branches
gh api repos/:owner/:repo/git/refs/heads --jq '.[].ref'

# Delete merged branches
git branch -d <branch>
git push origin --delete <branch>

# Stale branch alias
gh alias set stale-branches '!git branch -r --merged main | grep -v main'
```

### Cross-Platform Tips
- Use `.gitconfig` alias: `sync = !gh repo sync && git fetch -p`
- Windows: `git config --global core.autocrlf false` for team consistency

## Commit Quality

### Configure
```bash
# Signing
git config --global user.signingkey <GPG_KEY>
git config --global commit.gpgsign true

# Message template
git config commit.template ~/.gitmessage
```

### Craft Commits
```bash
# Selective staging
git add -p

# Commit with conventional message
git commit -m "feat(auth): add biometric fallback

Resolves #123. Adds TouchID/FaceID fallback for password-less login."
```

### Commit Conventions
| Prefix | Meaning |
|--------|---------|
| `feat:` | New feature |
| `fix:` | Bug fix |
| `refactor:` | Code refactoring |
| `perf:` | Performance improvement |
| `docs:` | Documentation |
| `test:` | Tests |
| `chore:` | Maintenance |
| `breaking:` | Breaking change |

### Link Issues
- Body: `Fixes #123` or `Refs #123` (auto-closes on merge)
- Verify context: `gh issue view <id>` before referencing

### Amend Safely
```bash
git commit --amend                    # Edit last message
git commit --amend --no-edit          # Add staged, keep message
git push --force-with-lease           # Safe force push (only for unpushed)
```

### Verify
```bash
git log --show-signature -1           # Check signature
gh pr status                          # Clean diff vs base
```

## Issue Triage

### List & Filter
```bash
gh issue list --label bug --state open --limit 50
gh issue list --state open --search "updated:<-30d"    # Stale issues
gh issue list --assignee @me --state open
```

### Create Issues
```bash
gh issue create --title "Crash on resume" \
  --body-file templates/bug.md \
  --label bug \
  --assignee @me \
  --project "Product Roadmap"
```

### Triage Existing
```bash
# View details
gh issue view 123 --json title,body,labels,comments

# Update labels
gh issue edit 123 --add-label priority/high --remove-label triage

# Assign owner
gh issue edit 123 --assignee user1

# Cross-link to PR
gh issue comment 123 --body "Tracking in PR #456"
```

### Export for Reporting
```bash
gh issue list --json number,title,state,assignees --jq \
  '.[] | [.number,.title,.state,(.assignees[].login? // "")] | @csv'
```

### Automation Aliases
```bash
gh alias set stale '!gh issue list --state open --search "updated:<-30d"'
gh alias set my-issues '!gh issue list --assignee @me --state open'
gh alias set triage '!gh issue list --label triage --state open --limit 20'
```

## PR Lifecycle

### Create PR
```bash
gh pr create --base main \
  --title "feat: add biometric login" \
  --body-file .github/pull_request_template.md \
  --reviewer user1,user2 \
  --label "area:core" \
  --project "Roadmap"
```

### Monitor & Update
```bash
# Preview diff
gh pr diff

# Watch CI checks
gh pr checks --watch

# Rerun failed jobs
gh run rerun <run-id>

# Add notes
gh pr comment --body "Updated screenshots for iOS"
```

### Review & Merge
```bash
# Checkout PR locally
gh pr checkout <number>

# Approve
gh pr review <number> --approve

# Request changes
gh pr review <number> --request-changes --body "See inline comments"

# Merge with rebase + delete branch
gh pr merge <number> --rebase --delete-branch
```

### Triage Open PRs
```bash
gh pr list --state open --label "needs-review"
gh pr list --state open --search "review:required"
```

### Automation Aliases
```bash
gh alias set pim '!gh pr status && gh pr checks'
gh alias set my-prs '!gh pr list --author @me --state open'
```

## Worktree Operations

### Setup
```bash
# Clone once
gh repo clone org/repo ~/code/repo
cd ~/code/repo && gh repo set-default

# Create worktree directory
mkdir -p ~/code/worktrees
```

### Create Worktrees
```bash
# From existing branch
git worktree add ../worktrees/feature-login feature/login-form

# Create new branch
git worktree add ../worktrees/bugfix-123 -b bugfix/123

# List all
git worktree list
```

### Sync & Cleanup
```bash
# Fetch all
gh repo sync
git fetch --all --prune

# Remove merged worktree
git worktree remove ../worktrees/bugfix-123
git branch -d bugfix/123

# Prune stale
git worktree prune
```

### Cross-Platform Paths
```bash
# macOS/Linux
git worktree add ~/code/worktrees/feature feature/branch

# Windows PowerShell
git worktree add $env:USERPROFILE\code\worktrees\feature feature/branch
```

### Tips
- Run CI from worktree: `gh workflow run` validates branch-specific tests
- Each worktree should track upstream: `git rev-parse --abbrev-ref @{u}`
- Audit weekly: remove stale worktrees, reclaim disk

## Verification
- `git status` clean before switching branches
- All new issues tagged with priority + area within SLA
- Every PR includes template, reviewers, and passing CI
- Branch list free of stale branches older than 30 days
- `git worktree list` shows only active tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
