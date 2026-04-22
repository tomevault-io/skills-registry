---
name: git-workflow
description: Git workflows, branching strategies, and DevOps practices Use when this capability is needed.
metadata:
  author: agenticdevops
---

# Git Workflow

Git operations and DevOps best practices.

## When to Use This Skill

Use this skill when:
- Managing code changes
- Creating pull requests
- Handling releases
- Resolving conflicts
- Debugging with git history

## Daily Operations

### Status & Info

```bash
# Current status
git status

# Short status
git status -s

# Current branch
git branch --show-current

# Recent commits
git log --oneline -10

# What changed today
git log --oneline --since="midnight"
```

### Sync with Remote

```bash
# Fetch updates (doesn't merge)
git fetch origin

# Pull with rebase (cleaner history)
git pull --rebase

# Pull specific branch
git pull origin main
```

### Commit Changes

```bash
# Stage specific files
git add file1.txt file2.txt

# Stage all changes
git add -A

# Interactive staging
git add -p

# Commit with message
git commit -m "feat: add user authentication"

# Amend last commit (before push)
git commit --amend

# Amend without changing message
git commit --amend --no-edit
```

## Branch Management

### Create & Switch

```bash
# Create and switch
git checkout -b feature/new-feature

# Or modern way
git switch -c feature/new-feature

# Switch to existing
git checkout main
git switch main

# From specific commit/branch
git checkout -b hotfix/bug-123 origin/main
```

### List Branches

```bash
# Local branches
git branch

# Remote branches
git branch -r

# All branches
git branch -a

# With last commit info
git branch -v
```

### Delete Branches

```bash
# Delete local (merged)
git branch -d feature/old-feature

# Force delete (unmerged)
git branch -D feature/abandoned

# Delete remote
git push origin --delete feature/old-feature

# Cleanup stale remote refs
git fetch --prune
```

## Pull Requests (GitHub CLI)

### Create PR

```bash
# Push branch first
git push -u origin feature/my-feature

# Create PR
gh pr create --title "Add feature X" --body "Description here"

# Create with template
gh pr create --fill

# Draft PR
gh pr create --draft
```

### Review PRs

```bash
# List PRs
gh pr list

# View specific PR
gh pr view 123

# Check out PR locally
gh pr checkout 123

# Diff
gh pr diff 123
```

### Merge PRs

```bash
# Merge
gh pr merge 123

# Squash merge
gh pr merge 123 --squash

# Rebase merge
gh pr merge 123 --rebase

# Delete branch after merge
gh pr merge 123 --delete-branch
```

## Hotfix Workflow

```bash
# 1. Create hotfix branch from main/production
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug-123

# 2. Make fix
git add .
git commit -m "fix: resolve critical bug #123"

# 3. Push and create PR
git push -u origin hotfix/critical-bug-123
gh pr create --title "Hotfix: Critical bug #123" --base main

# 4. After merge, tag release
git checkout main
git pull origin main
git tag -a v1.2.1 -m "Hotfix release v1.2.1"
git push origin v1.2.1
```

## Release Management

### Tagging

```bash
# Create annotated tag
git tag -a v1.2.0 -m "Release v1.2.0"

# List tags
git tag -l

# List with pattern
git tag -l "v1.*"

# Push tags
git push origin v1.2.0
git push origin --tags  # all tags

# Delete tag
git tag -d v1.2.0
git push origin --delete v1.2.0
```

### Changelog from Commits

```bash
# Commits since last tag
git log $(git describe --tags --abbrev=0)..HEAD --oneline

# Formatted for changelog
git log $(git describe --tags --abbrev=0)..HEAD --pretty=format:"- %s"
```

## Debugging with Git

### Find Who Changed What

```bash
# Blame (who changed each line)
git blame file.txt

# Blame specific lines
git blame -L 10,20 file.txt

# Ignore whitespace
git blame -w file.txt
```

### Find When Bug Introduced

```bash
# Binary search
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
# Test each commit, mark good/bad
git bisect good  # or git bisect bad
# When done
git bisect reset
```

### Search History

```bash
# Search commit messages
git log --grep="bug fix"

# Search code changes
git log -S "function_name" --oneline

# Search with regex
git log -G "regex_pattern"

# Show what changed in commits
git log -p --grep="feature"
```

## Undo Operations

### Unstage Files

```bash
# Unstage file
git restore --staged file.txt

# Old way
git reset HEAD file.txt
```

### Discard Changes

```bash
# Discard working directory changes
git restore file.txt

# Discard all changes
git restore .

# Old way
git checkout -- file.txt
```

### Reset Commits

```bash
# Soft reset (keep changes staged)
git reset --soft HEAD~1

# Mixed reset (keep changes unstaged)
git reset HEAD~1

# Hard reset (discard changes - DANGEROUS)
git reset --hard HEAD~1
```

### Revert (Safe for Shared History)

```bash
# Create new commit that undoes
git revert HEAD

# Revert specific commit
git revert abc1234

# Revert without auto-commit
git revert --no-commit abc1234
```

## Stashing

```bash
# Stash changes
git stash

# Stash with message
git stash push -m "WIP: feature X"

# List stashes
git stash list

# Apply latest stash
git stash pop

# Apply specific stash
git stash apply stash@{1}

# Drop stash
git stash drop stash@{0}
```

## Merge & Rebase

### Merge

```bash
# Merge branch into current
git merge feature/new-feature

# Merge with commit (no fast-forward)
git merge --no-ff feature/new-feature

# Abort merge
git merge --abort
```

### Rebase

```bash
# Rebase current onto main
git rebase main

# Interactive rebase (edit history)
git rebase -i HEAD~3

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

### Conflict Resolution

```bash
# See conflicting files
git status

# After editing conflicts
git add resolved-file.txt
git rebase --continue  # or git merge --continue
```

## Configuration

```bash
# User setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Default branch
git config --global init.defaultBranch main

# Useful aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --all"
```

## Quick Reference

```bash
# Morning sync
git fetch --all && git pull --rebase

# Feature branch workflow
git checkout -b feature/X && [make changes] && git add . && git commit -m "feat: X" && git push -u origin feature/X && gh pr create

# Quick fixup
git add . && git commit --amend --no-edit && git push --force-with-lease

# Clean merged branches
git branch --merged | grep -v "main\|master" | xargs git branch -d
```

## Related Skills

- **terraform-workflow**: For IaC version control
- **k8s-deploy**: For deployment workflows
- **incident-response**: For hotfix procedures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
