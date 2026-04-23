---
name: git-workflow
description: Git best practices for clean history, branching strategies, and team collaboration. Use when writing commit messages, creating branches, making pull requests, or when user asks about "git workflow", "commit conventions", "branching strategy", or "PR review". Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Git Workflow

Maintain a clean, useful Git history and collaborate effectively.

## Commit Messages

### Rules

- ✅ DO: Use conventional commits format
- ✅ DO: Start with a verb in imperative mood ("Add", not "Added")
- ✅ DO: Keep subject line under 50 characters
- ✅ DO: Add body for complex changes
- ❌ DON'T: Write vague messages ("fix bug", "update")
- ❌ DON'T: Mix unrelated changes in one commit

### Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

### Types

| Type       | Description                     |
| ---------- | ------------------------------- |
| `feat`     | New feature                     |
| `fix`      | Bug fix                         |
| `docs`     | Documentation only              |
| `style`    | Formatting, no code change      |
| `refactor` | Code change without feature/fix |
| `perf`     | Performance improvement         |
| `test`     | Adding/updating tests           |
| `chore`    | Build, tooling, deps            |

### Examples

```bash
# ❌ Bad
git commit -m "fix"
git commit -m "update stuff"
git commit -m "WIP"

# ✅ Good
git commit -m "feat(auth): add password reset flow"
git commit -m "fix(api): handle null response from external service"
git commit -m "docs: add API authentication guide"

# ✅ Good - with body
git commit -m "refactor(cart): extract price calculation

- Move calculateTotal to separate module
- Add support for percentage and fixed discounts
- Add comprehensive unit tests

Closes #123"
```

## Branch Naming

### Rules

- ✅ DO: Use prefixes (`feature/`, `fix/`, `chore/`)
- ✅ DO: Include ticket number if applicable
- ✅ DO: Use kebab-case
- ✅ DO: Keep names descriptive but concise
- ❌ DON'T: Use personal names or dates
- ❌ DON'T: Use spaces or special characters

### Format

```
<type>/<ticket>-<description>
```

### Examples

```bash
# ❌ Bad
my-branch
fix
john-working-on-stuff
2024-01-15-updates

# ✅ Good
feature/AUTH-123-password-reset
fix/API-456-null-response-handling
chore/upgrade-dependencies
refactor/extract-validation-utils
```

## Pull Requests

### Rules

- ✅ DO: Keep PRs focused and small (<400 lines ideally)
- ✅ DO: Write descriptive PR titles and descriptions
- ✅ DO: Link related issues
- ✅ DO: Request review from relevant team members
- ❌ DON'T: Mix features with refactoring
- ❌ DON'T: Leave PRs open for too long

### PR Template

```markdown
## Summary

Brief description of changes

## Changes

- Change 1
- Change 2

## Testing

- [ ] Unit tests added/updated
- [ ] Manual testing performed
- [ ] Edge cases considered

## Screenshots (if UI changes)

[Add screenshots]

## Related Issues

Closes #123
```

## Keeping History Clean

### Rules

- ✅ DO: Squash WIP commits before merging
- ✅ DO: Rebase feature branches on main
- ✅ DO: Use interactive rebase to clean up
- ❌ DON'T: Merge with broken/WIP commits
- ❌ DON'T: Force push to shared branches

### Examples

```bash
# Squash last 3 commits
git rebase -i HEAD~3

# Rebase on main before PR
git fetch origin
git rebase origin/main

# Amend last commit
git commit --amend

# Fixup a previous commit
git commit --fixup <commit-hash>
git rebase -i --autosquash <commit-hash>^
```

## Git Workflow Strategies

### GitHub Flow (Simple)

```
main (always deployable)
  └── feature/xyz
        └── (PR) → main
```

1. Branch from `main`
2. Make changes
3. Open PR
4. Review & merge to `main`
5. Deploy `main`

### Trunk-Based Development

```
main (always deployable)
  ├── short-lived-branch-1 (< 1 day)
  ├── short-lived-branch-2 (< 1 day)
  └── Use feature flags for WIP
```

## Useful Commands

```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Discard all local changes
git checkout -- .
git clean -fd

# Stash changes
git stash
git stash pop

# Cherry-pick a commit
git cherry-pick <commit-hash>

# Find commit that introduced bug
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

# View file at specific commit
git show <commit>:<file>

# Blame with ignore whitespace
git blame -w <file>

# Log with graph
git log --oneline --graph --all
```

## .gitignore Best Practices

```gitignore
# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
.next/

# Environment
.env
.env.local
.env.*.local

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Test coverage
coverage/

# Temporary
tmp/
temp/
```

## Handling Conflicts

```bash
# During rebase
git rebase origin/main
# ... resolve conflicts ...
git add <resolved-files>
git rebase --continue

# Abort if needed
git rebase --abort

# During merge
git merge feature-branch
# ... resolve conflicts ...
git add <resolved-files>
git commit
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
