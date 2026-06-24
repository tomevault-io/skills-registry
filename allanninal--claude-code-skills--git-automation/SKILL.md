---
name: git-automation
description: Automate git operations - commits, branches, rebasing, cherry-picking, and repository management. Use when streamlining git workflows or automating repetitive git tasks. Use when this capability is needed.
metadata:
  author: allanninal
---

# Git Automation

## When to Use This Skill

- Automating commit workflows
- Managing branches at scale
- Scripting git operations
- Setting up git hooks
- Cleaning up repositories
- Handling complex rebasing scenarios

## Commit Automation

### Smart Commit Messages

```bash
#!/bin/bash
# git-smart-commit.sh - Generate commit message from changes

# Get changed files
CHANGED=$(git diff --cached --name-only)

# Detect type from files
if echo "$CHANGED" | grep -q "test"; then
  TYPE="test"
elif echo "$CHANGED" | grep -q "\.md$"; then
  TYPE="docs"
elif echo "$CHANGED" | grep -q "package.json\|requirements.txt"; then
  TYPE="deps"
else
  TYPE="feat"
fi

# Get scope from directory
SCOPE=$(echo "$CHANGED" | head -1 | cut -d'/' -f1)

# Generate message
echo "Suggested commit: $TYPE($SCOPE): "
git diff --cached --stat
```

### Conventional Commit Helper

```bash
#!/bin/bash
# commit.sh - Interactive conventional commit

PS3="Select commit type: "
types=("feat" "fix" "docs" "style" "refactor" "perf" "test" "build" "ci" "chore")

select type in "${types[@]}"; do
  break
done

read -p "Scope (optional): " scope
read -p "Description: " desc
read -p "Breaking change? (y/n): " breaking

if [ "$breaking" = "y" ]; then
  type="$type!"
fi

if [ -n "$scope" ]; then
  msg="$type($scope): $desc"
else
  msg="$type: $desc"
fi

git commit -m "$msg"
```

## Branch Management

### Branch Cleanup

```bash
#!/bin/bash
# cleanup-branches.sh - Remove merged branches

# Local branches merged to main
git branch --merged main | grep -v "main\|master\|develop" | xargs -r git branch -d

# Remote branches merged to main
git fetch --prune
git branch -r --merged origin/main | \
  grep -v "main\|master\|develop\|HEAD" | \
  sed 's/origin\///' | \
  xargs -r -I {} git push origin --delete {}

echo "Cleaned up merged branches"
```

### Branch from Issue

```bash
#!/bin/bash
# branch-from-issue.sh - Create branch from GitHub issue

ISSUE_NUMBER=$1

if [ -z "$ISSUE_NUMBER" ]; then
  echo "Usage: branch-from-issue.sh <issue-number>"
  exit 1
fi

# Get issue title from GitHub
TITLE=$(gh issue view "$ISSUE_NUMBER" --json title -q '.title')

# Sanitize for branch name
BRANCH_NAME=$(echo "$TITLE" | \
  tr '[:upper:]' '[:lower:]' | \
  tr ' ' '-' | \
  tr -cd '[:alnum:]-' | \
  cut -c1-50)

BRANCH="feature/$ISSUE_NUMBER-$BRANCH_NAME"

git checkout -b "$BRANCH"
echo "Created branch: $BRANCH"
```

### Sync All Branches

```bash
#!/bin/bash
# sync-branches.sh - Update all local branches from remote

git fetch --all --prune

for branch in $(git branch --format='%(refname:short)'); do
  if git show-ref --verify --quiet "refs/remotes/origin/$branch"; then
    git checkout "$branch"
    git pull --rebase origin "$branch"
  fi
done

git checkout main
echo "All branches synced"
```

## Git Hooks

### Pre-commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Run linters
echo "Running linters..."
npm run lint || exit 1

# Check for secrets
echo "Checking for secrets..."
if git diff --cached | grep -iE "(password|secret|api_key|token)\s*=\s*['\"][^'\"]+['\"]"; then
  echo "ERROR: Possible secrets detected!"
  exit 1
fi

# Check for console.log
if git diff --cached --name-only | xargs grep -l "console.log" 2>/dev/null; then
  echo "WARNING: console.log statements found"
  read -p "Continue anyway? (y/n) " -n 1 -r
  echo
  if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    exit 1
  fi
fi

# Run tests for changed files
echo "Running tests..."
npm test -- --findRelatedTests $(git diff --cached --name-only) || exit 1

echo "Pre-commit checks passed!"
```

### Commit-msg Hook

```bash
#!/bin/bash
# .git/hooks/commit-msg - Validate conventional commits

MSG=$(cat "$1")
PATTERN="^(feat|fix|docs|style|refactor|perf|test|build|ci|chore)(\(.+\))?(!)?: .{1,50}"

if ! echo "$MSG" | grep -qE "$PATTERN"; then
  echo "ERROR: Commit message doesn't follow conventional commits format"
  echo "Expected: type(scope): description"
  echo "Example: feat(auth): add OAuth2 support"
  exit 1
fi
```

### Pre-push Hook

```bash
#!/bin/bash
# .git/hooks/pre-push

# Prevent push to main/master
BRANCH=$(git rev-parse --abbrev-ref HEAD)

if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
  echo "ERROR: Direct push to $BRANCH is not allowed"
  echo "Please create a feature branch and PR"
  exit 1
fi

# Run full test suite before push
echo "Running full test suite..."
npm test || exit 1

echo "Pre-push checks passed!"
```

## Rebase & Merge Automation

### Interactive Rebase Helper

```bash
#!/bin/bash
# squash-commits.sh - Squash last N commits

COUNT=${1:-$(git rev-list --count HEAD ^main)}

echo "Squashing last $COUNT commits..."
git rebase -i HEAD~$COUNT
```

### Auto-rebase on Main

```bash
#!/bin/bash
# rebase-on-main.sh

BRANCH=$(git rev-parse --abbrev-ref HEAD)

git fetch origin main
git rebase origin/main

if [ $? -ne 0 ]; then
  echo "Rebase conflicts detected. Resolve and run:"
  echo "  git rebase --continue"
  exit 1
fi

echo "Successfully rebased $BRANCH on main"
```

### Cherry-pick Range

```bash
#!/bin/bash
# cherry-pick-range.sh - Cherry-pick commits from one branch to another

SOURCE_BRANCH=$1
TARGET_BRANCH=$2
START_COMMIT=$3
END_COMMIT=${4:-HEAD}

git checkout "$TARGET_BRANCH"
git cherry-pick "$START_COMMIT"^.."$END_COMMIT"

if [ $? -ne 0 ]; then
  echo "Cherry-pick conflicts. Resolve and run:"
  echo "  git cherry-pick --continue"
  exit 1
fi

echo "Successfully cherry-picked commits to $TARGET_BRANCH"
```

## Repository Maintenance

### Garbage Collection

```bash
#!/bin/bash
# git-cleanup.sh - Full repository cleanup

echo "Cleaning up repository..."

# Remove untracked files
git clean -fd

# Prune remote tracking branches
git remote prune origin

# Garbage collection
git gc --aggressive --prune=now

# Verify repository
git fsck --full

echo "Repository cleanup complete"
```

### Find Large Files

```bash
#!/bin/bash
# find-large-files.sh - Find large files in git history

git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort -rnk2 | \
  head -20 | \
  cut -d' ' -f2- | \
  numfmt --field=1 --to=iec-i --suffix=B --padding=7
```

### Remove Sensitive Data

```bash
#!/bin/bash
# remove-file-from-history.sh - Remove file from entire history

FILE=$1

git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch $FILE" \
  --prune-empty --tag-name-filter cat -- --all

git push origin --force --all
git push origin --force --tags
```

## Aliases

```bash
# ~/.gitconfig

[alias]
  # Quick status
  s = status -sb

  # Amend without editing message
  amend = commit --amend --no-edit

  # Undo last commit (keep changes)
  undo = reset HEAD~1 --soft

  # Interactive add
  ia = add -p

  # Pretty log
  lg = log --oneline --graph --decorate -20

  # Show changed files
  changed = diff --name-only

  # List branches by date
  recent = branch --sort=-committerdate --format='%(committerdate:short) %(refname:short)'

  # Sync with main
  sync = !git fetch origin && git rebase origin/main

  # Clean merged branches
  cleanup = !git branch --merged | grep -v main | xargs -r git branch -d
```

## Workflow Scripts

### Daily Workflow

```bash
#!/bin/bash
# daily-git.sh - Daily git routine

echo "=== Daily Git Routine ==="

# Update main
git checkout main
git pull --rebase

# Return to previous branch
git checkout -

# Rebase on main
git rebase main

# Show status
git status
git log --oneline -5
```

### Release Workflow

```bash
#!/bin/bash
# release.sh - Create release

VERSION=$1

if [ -z "$VERSION" ]; then
  echo "Usage: release.sh <version>"
  exit 1
fi

git checkout main
git pull

# Create release branch
git checkout -b "release/$VERSION"

# Update version files
npm version "$VERSION" --no-git-tag-version

git add .
git commit -m "chore: bump version to $VERSION"

# Create tag
git tag -a "v$VERSION" -m "Release $VERSION"

# Push
git push origin "release/$VERSION"
git push origin "v$VERSION"

echo "Release $VERSION created!"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
