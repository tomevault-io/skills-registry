---
name: git-ship
description: Automate git workflow including add, commit, push, and PR creation. Use this skill when user says "ship it", "git ship", "/ship", "commit and push", "create PR", or asks to push changes and create a pull request. Handles the complete flow from staging changes to opening a PR on GitHub. Use when this capability is needed.
metadata:
  author: kawabeshogo
---

# Git Ship

Automate the complete git workflow: stage changes, generate commit message, push to remote, and create a pull request.

## Workflow

### 1. Check Git Status

Run these commands to understand current state:

```bash
git status
git diff --stat
git log --oneline -5
```

### 2. Ask for Target Branch

Ask the user which branch to target for the PR using AskUserQuestion:

- Detect available remote branches (main, master, develop) as options
- Let user specify a custom branch if needed

### 3. Stage Changes

Stage all changes or ask user to confirm which files to include:

```bash
git add -A
```

For selective staging, show the list of changed files and let user choose.

### 4. Generate Commit Message

Analyze the diff to generate a meaningful commit message:

```bash
git diff --cached
```

Generate a commit message following conventional commits format:

- `feat:` for new features
- `fix:` for bug fixes
- `docs:` for documentation changes
- `refactor:` for code refactoring
- `style:` for formatting changes
- `test:` for adding tests
- `chore:` for maintenance tasks

Commit message structure:

```
<type>: <short summary>

<detailed description if needed>

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 5. Commit Changes

```bash
git commit -m "$(cat <<'EOF'
<generated message>

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

### 6. Push to Remote

Check if branch exists on remote and push:

```bash
# Check current branch
git branch --show-current

# Push with upstream tracking
git push -u origin HEAD
```

### 7. Create Pull Request

Use GitHub CLI to create the PR:

```bash
gh pr create --title "<PR title>" --body "$(cat <<'EOF'
## Summary
<bullet points summarizing changes>

## Changes
<list of modified files/features>

## Test Plan
<how to verify the changes>

---
Generated with Claude
EOF
)"
```

## Error Handling

- **No changes**: If `git status` shows nothing to commit, inform user
- **Uncommitted changes on wrong branch**: Offer to create a new branch first
- **Push rejected**: Pull latest changes and retry, or ask user for guidance
- **PR creation fails**: Check if `gh` CLI is authenticated, guide user through `gh auth login`

## Quick Reference

| Step      | Command                   |
| --------- | ------------------------- |
| Status    | `git status`              |
| Stage all | `git add -A`              |
| Commit    | `git commit -m "message"` |
| Push      | `git push -u origin HEAD` |
| Create PR | `gh pr create`            |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kawabeshogo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
