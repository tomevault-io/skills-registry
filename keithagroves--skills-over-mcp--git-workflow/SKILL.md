---
name: git-workflow
description: Best practices for Git version control workflows including branching strategies, commit conventions, and collaboration patterns. Use when working with Git repositories, managing branches, or coordinating team development. Use when this capability is needed.
metadata:
  author: keithagroves
---

# Git Workflow Skill

This skill provides guidance on effective Git workflows and best practices.

## When to Use

- Starting work on a new feature or fix
- Managing branches and merges
- Writing commit messages
- Resolving merge conflicts
- Setting up repository workflows

## Branch Naming Conventions

Use descriptive, prefixed branch names:

```
feature/add-user-authentication
bugfix/fix-login-redirect
hotfix/security-patch-cve-2024
refactor/extract-payment-module
docs/update-api-documentation
```

## Commit Message Format

Follow conventional commits:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance tasks

### Examples

```
feat(auth): add OAuth2 login support

Implement OAuth2 authentication flow with Google and GitHub providers.
Includes token refresh and secure storage.

Closes #123
```

```
fix(api): handle null response from payment gateway

The payment gateway occasionally returns null for declined cards.
Added null check and appropriate error message.

Fixes #456
```

## Common Workflows

### Feature Branch Workflow

```bash
# Start a new feature
git checkout main
git pull origin main
git checkout -b feature/my-feature

# Work on the feature
git add .
git commit -m "feat: implement feature"

# Keep up to date with main
git fetch origin
git rebase origin/main

# Push and create PR
git push -u origin feature/my-feature
```

### Fixing a Bug

```bash
# Create bugfix branch
git checkout -b bugfix/issue-description

# Make the fix
git add .
git commit -m "fix: resolve issue description"

# Push for review
git push -u origin bugfix/issue-description
```

### Handling Merge Conflicts

```bash
# Update your branch
git fetch origin
git rebase origin/main

# If conflicts occur:
# 1. Edit conflicted files
# 2. Mark as resolved
git add <resolved-files>
git rebase --continue

# If you need to abort
git rebase --abort
```

## Best Practices

### Do

- Commit early and often
- Write meaningful commit messages
- Keep commits focused (one logical change)
- Pull/rebase before pushing
- Review your changes before committing

### Don't

- Commit sensitive data (passwords, keys)
- Force push to shared branches
- Commit generated files
- Make huge commits with unrelated changes
- Leave work uncommitted for long periods

## Useful Commands

```bash
# See what changed
git status
git diff

# Interactive staging
git add -p

# Amend last commit (before push)
git commit --amend

# View history
git log --oneline --graph

# Stash changes temporarily
git stash
git stash pop

# Undo last commit (keep changes)
git reset --soft HEAD~1
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keithagroves) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
