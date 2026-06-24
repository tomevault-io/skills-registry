---
name: git-workflow-advanced
description: Advanced git workflows for teams - branching strategies (Git Flow, GitHub Flow, trunk-based), PR templates, commit conventions, conflict resolution, and release management. Use when this capability is needed.
metadata:
  author: dinoudon
---

# Git Workflow Advanced

## Overview

Master advanced git workflows for professional team collaboration.

**Key workflows:**
- **Git Flow** - Feature branches, release branches, hotfixes
- **GitHub Flow** - Simple, deploy-focused workflow
- **Trunk-Based Development** - Short-lived branches, continuous integration
- **Release Management** - Versioning, changelogs, tags

**Core principles:**
- Main branch is always deployable
- Feature branches are short-lived (< 3 days)
- Commits are atomic and descriptive
- Pull requests are small and focused
- Code review is mandatory

## When to Use

**Git Flow:**
- Scheduled releases (monthly, quarterly)
- Multiple versions in production
- Large teams (10+ developers)
- Enterprise software

**GitHub Flow:**
- Continuous deployment
- Small teams (2-10 developers)
- Web applications
- Fast iteration

**Trunk-Based Development:**
- Very frequent deployments (multiple per day)
- Mature CI/CD pipeline
- High-trust teams
- Feature flags for incomplete work

## Git Flow Workflow

### Branch Structure

```
main (production)
  ├── develop (integration)
  │   ├── feature/user-auth
  │   ├── feature/payment
  │   └── feature/notifications
  ├── release/v1.2.0
  └── hotfix/critical-bug
```

### Branch Types

**1. Main Branch**
- Production-ready code
- Tagged with version numbers
- Never commit directly
- Only merge from release or hotfix

**2. Develop Branch**
- Integration branch
- Latest development changes
- Base for feature branches
- Merge features here first

**3. Feature Branches**
- New features or enhancements
- Branch from: `develop`
- Merge to: `develop`
- Naming: `feature/feature-name`

**4. Release Branches**
- Prepare for production release
- Branch from: `develop`
- Merge to: `main` and `develop`
- Naming: `release/v1.2.0`

**5. Hotfix Branches**
- Critical production fixes
- Branch from: `main`
- Merge to: `main` and `develop`
- Naming: `hotfix/critical-bug`

### Feature Branch Workflow

```bash
# 1. Create feature branch from develop
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication

# 2. Work on feature (commit frequently)
git add src/auth/
git commit -m "feat: add login endpoint"
git push origin feature/user-authentication

# 3. Keep branch updated with develop
git checkout develop
git pull origin develop
git checkout feature/user-authentication
git merge develop
# Or: git rebase develop (for cleaner history)

# 4. Create pull request
gh pr create --base develop --head feature/user-authentication \
  --title "Add user authentication" \
  --body "Implements login, signup, and password reset"

# 5. After approval, merge to develop
gh pr merge --squash
# Or: gh pr merge --merge (preserve commits)
# Or: gh pr merge --rebase (linear history)

# 6. Delete feature branch
git branch -d feature/user-authentication
git push origin --delete feature/user-authentication
```

### Release Branch Workflow

```bash
# 1. Create release branch from develop
git checkout develop
git pull origin develop
git checkout -b release/v1.2.0

# 2. Bump version number
npm version minor  # 1.1.0 -> 1.2.0
git add package.json package-lock.json
git commit -m "chore: bump version to 1.2.0"

# 3. Update CHANGELOG.md
# Add release notes, breaking changes, new features
git add CHANGELOG.md
git commit -m "docs: update changelog for v1.2.0"

# 4. Bug fixes only (no new features)
git add src/bugfix.ts
git commit -m "fix: resolve login issue"

# 5. Merge to main (production)
git checkout main
git pull origin main
git merge --no-ff release/v1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin main --tags

# 6. Merge back to develop
git checkout develop
git merge --no-ff release/v1.2.0
git push origin develop

# 7. Delete release branch
git branch -d release/v1.2.0
git push origin --delete release/v1.2.0
```

### Hotfix Branch Workflow

```bash
# 1. Create hotfix branch from main
git checkout main
git pull origin main
git checkout -b hotfix/critical-security-fix

# 2. Fix the issue
git add src/security/
git commit -m "fix: patch SQL injection vulnerability"

# 3. Bump patch version
npm version patch  # 1.2.0 -> 1.2.1
git add package.json
git commit -m "chore: bump version to 1.2.1"

# 4. Merge to main
git checkout main
git merge --no-ff hotfix/critical-security-fix
git tag -a v1.2.1 -m "Hotfix: Security patch"
git push origin main --tags

# 5. Merge to develop
git checkout develop
git merge --no-ff hotfix/critical-security-fix
git push origin develop

# 6. Delete hotfix branch
git branch -d hotfix/critical-security-fix
git push origin --delete hotfix/critical-security-fix
```

---

## GitHub Flow Workflow

### Simplified Structure

```
main (production)
  ├── feature/user-auth
  ├── feature/payment
  └── fix/login-bug
```

### Workflow Steps

```bash
# 1. Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/user-authentication

# 2. Work and commit frequently
git add .
git commit -m "feat: add login form"
git push origin feature/user-authentication

# 3. Create pull request (early and often)
gh pr create --base main --head feature/user-authentication \
  --title "Add user authentication" \
  --body "WIP: Login form implemented, signup next"

# 4. Continue working, push updates
git add .
git commit -m "feat: add signup form"
git push origin feature/user-authentication

# 5. Request review when ready
gh pr ready  # Mark PR as ready for review

# 6. Address review comments
git add .
git commit -m "refactor: improve validation logic"
git push origin feature/user-authentication

# 7. Merge to main (after approval)
gh pr merge --squash

# 8. Deploy to production (automatic via CI/CD)
# GitHub Actions deploys main branch automatically

# 9. Delete feature branch
git branch -d feature/user-authentication
```

### Key Differences from Git Flow

**Simpler:**
- Only one long-lived branch (main)
- No develop branch
- No release branches

**Faster:**
- Deploy directly from main
- No waiting for release windows
- Hotfixes are just regular branches

**Best for:**
- Web applications
- Continuous deployment
- Small teams

---

## Trunk-Based Development

### Workflow

```bash
# 1. Create short-lived branch (< 1 day)
git checkout main
git pull origin main
git checkout -b add-login-button

# 2. Make small change
git add src/components/LoginButton.tsx
git commit -m "feat: add login button component"

# 3. Push and create PR immediately
git push origin add-login-button
gh pr create --base main --head add-login-button

# 4. Merge quickly (within hours)
gh pr merge --squash

# 5. Delete branch immediately
git branch -d add-login-button

# 6. Next change, new branch
git checkout main
git pull origin main
git checkout -b add-logout-button
# Repeat...
```

### Feature Flags for Incomplete Work

```typescript
// Use feature flags to hide incomplete features
const ENABLE_NEW_AUTH = process.env.ENABLE_NEW_AUTH === 'true';

export function LoginPage() {
  if (ENABLE_NEW_AUTH) {
    return <NewAuthFlow />;  // Incomplete, hidden in production
  }
  return <OldAuthFlow />;    // Current production code
}

// Merge to main even though NewAuthFlow is incomplete
// Enable flag when ready
```

### Benefits

**Faster integration:**
- No long-lived branches
- No merge conflicts
- Continuous integration

**Simpler workflow:**
- One branch (main)
- No branch management overhead
- Easy to understand

**Requirements:**
- Strong CI/CD pipeline
- Comprehensive test coverage
- Feature flags for incomplete work
- High-trust team

---

## Commit Conventions

### Conventional Commits Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

```
feat:     New feature
fix:      Bug fix
docs:     Documentation only
style:    Formatting, missing semicolons, etc.
refactor: Code restructuring (no behavior change)
perf:     Performance improvement
test:     Add or update tests
build:    Build system or dependencies
ci:       CI/CD configuration
chore:    Maintenance tasks
revert:   Revert previous commit
```

### Examples

**Good commits:**

```bash
# Feature
git commit -m "feat(auth): add password reset functionality

Implements password reset via email link.
Users can request reset, receive email, and set new password.

Closes #123"

# Bug fix
git commit -m "fix(api): resolve race condition in user creation

Added transaction to ensure atomic user creation.
Prevents duplicate users when multiple requests arrive simultaneously.

Fixes #456"

# Breaking change
git commit -m "feat(api): change authentication to JWT

BREAKING CHANGE: Session-based auth is removed.
Clients must now use JWT tokens in Authorization header.

Migration guide: docs/migration-v2.md"

# Refactor
git commit -m "refactor(auth): extract validation logic

Moved validation from controller to separate module.
No behavior change, improves testability."

# Documentation
git commit -m "docs(readme): add installation instructions

Added step-by-step guide for local development setup."
```

**Bad commits:**

```bash
# Too vague
git commit -m "updates"
git commit -m "fix stuff"
git commit -m "WIP"

# Too much in one commit
git commit -m "feat: add auth, fix bugs, update docs, refactor code"

# No context
git commit -m "fix"
git commit -m "changes"
```

### Commit Message Template

```bash
# Create commit template
cat > ~/.gitmessage << 'EOF'
# <type>(<scope>): <subject>
# |<----  Using a Maximum Of 50 Characters  ---->|

# Explain why this change is being made
# |<----   Try To Limit Each Line to a Maximum Of 72 Characters   ---->|

# Provide links or keys to any relevant tickets, articles or other resources
# Example: Closes #123

# --- COMMIT END ---
# Type can be:
#   feat     (new feature)
#   fix      (bug fix)
#   refactor (refactoring code)
#   style    (formatting, missing semicolons, etc.)
#   docs     (changes to documentation)
#   test     (adding or refactoring tests)
#   chore    (updating build tasks, package manager configs, etc.)
# --------------------
# Remember to:
#   - Capitalize the subject line
#   - Use the imperative mood in the subject line
#   - Do not end the subject line with a period
#   - Separate subject from body with a blank line
#   - Use the body to explain what and why vs. how
#   - Can use multiple lines with "-" for bullet points in body
EOF

# Configure git to use template
git config --global commit.template ~/.gitmessage
```

---

## Pull Request Best Practices

### PR Template

```markdown
<!-- .github/pull_request_template.md -->

## Description
<!-- What does this PR do? Why is it needed? -->

## Type of Change
- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## How Has This Been Tested?
<!-- Describe the tests you ran -->
- [ ] Unit tests
- [ ] Integration tests
- [ ] Manual testing

## Checklist
- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published

## Screenshots (if applicable)
<!-- Add screenshots to help explain your changes -->

## Related Issues
<!-- Link to related issues: Closes #123, Fixes #456 -->
```

### PR Size Guidelines

**Small PR (Ideal):**
- 1-3 files changed
- < 200 lines added/removed
- Single focused change
- Easy to review (< 15 minutes)

**Medium PR (Acceptable):**
- 4-10 files changed
- 200-500 lines added/removed
- Related changes
- Reviewable in 30 minutes

**Large PR (Avoid):**
- 10+ files changed
- 500+ lines added/removed
- Multiple unrelated changes
- Hard to review (> 1 hour)

**How to keep PRs small:**
- Break features into smaller pieces
- Use feature flags for incomplete work
- Separate refactoring from features
- Submit documentation separately

### PR Review Process

```bash
# 1. Create PR
gh pr create --base main --head feature/user-auth \
  --title "Add user authentication" \
  --body "$(cat pr-description.md)"

# 2. Request reviewers
gh pr edit --add-reviewer @teammate1,@teammate2

# 3. Address review comments
# Make changes based on feedback
git add .
git commit -m "refactor: improve error handling per review"
git push origin feature/user-auth

# 4. Re-request review
gh pr review --approve  # Reviewer approves

# 5. Merge PR
gh pr merge --squash --delete-branch

# 6. Verify deployment
# Check CI/CD pipeline, monitor production
```

---

## Conflict Resolution

### Merge Conflicts

```bash
# Scenario: Your branch conflicts with main

# 1. Update main
git checkout main
git pull origin main

# 2. Merge main into your branch
git checkout feature/user-auth
git merge main

# 3. Git shows conflicts
# CONFLICT (content): Merge conflict in src/auth.ts
# Automatic merge failed; fix conflicts and then commit the result.

# 4. Open conflicted file
# <<<<<<< HEAD (your changes)
# Your code here
# =======
# Their code here
# >>>>>>> main

# 5. Resolve conflict (choose one or combine)
# Remove conflict markers, keep desired code

# 6. Mark as resolved
git add src/auth.ts

# 7. Complete merge
git commit -m "merge: resolve conflicts with main"

# 8. Push
git push origin feature/user-auth
```

### Rebase Conflicts

```bash
# Scenario: Rebase your branch onto main

# 1. Update main
git checkout main
git pull origin main

# 2. Rebase your branch
git checkout feature/user-auth
git rebase main

# 3. Git shows conflicts
# CONFLICT (content): Merge conflict in src/auth.ts

# 4. Resolve conflict (same as merge)
# Edit file, remove conflict markers

# 5. Mark as resolved
git add src/auth.ts

# 6. Continue rebase
git rebase --continue

# 7. Force push (rebase rewrites history)
git push origin feature/user-auth --force-with-lease
```

### Merge vs Rebase

**Use Merge when:**
- Working on shared branches
- Want to preserve history
- Collaborating with others

**Use Rebase when:**
- Working on personal feature branches
- Want clean linear history
- Before creating PR

**Never rebase:**
- Public branches (main, develop)
- Shared feature branches
- After pushing to remote (unless alone on branch)

---

## Release Management

### Semantic Versioning

```
MAJOR.MINOR.PATCH

Example: 2.3.1
- MAJOR: 2 (breaking changes)
- MINOR: 3 (new features, backward compatible)
- PATCH: 1 (bug fixes, backward compatible)
```

**When to bump:**
- **MAJOR:** Breaking changes (API changes, removed features)
- **MINOR:** New features (backward compatible)
- **PATCH:** Bug fixes (backward compatible)

### Creating a Release

```bash
# 1. Update version
npm version minor  # 1.2.0 -> 1.3.0
# Or: npm version major  # 1.2.0 -> 2.0.0
# Or: npm version patch  # 1.2.0 -> 1.2.1

# 2. Update CHANGELOG.md
cat >> CHANGELOG.md << 'EOF'
## [1.3.0] - 2026-05-26

### Added
- User authentication with JWT
- Password reset functionality
- Email verification

### Changed
- Improved error messages
- Updated dependencies

### Fixed
- Login race condition
- Memory leak in session management

### Breaking Changes
- Removed session-based auth (use JWT instead)
EOF

git add CHANGELOG.md package.json package-lock.json
git commit -m "chore: release v1.3.0"

# 3. Create git tag
git tag -a v1.3.0 -m "Release version 1.3.0"

# 4. Push to remote
git push origin main --tags

# 5. Create GitHub release
gh release create v1.3.0 \
  --title "v1.3.0" \
  --notes-file CHANGELOG.md \
  --latest

# 6. Publish to npm (if applicable)
npm publish
```

### CHANGELOG.md Format

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]
### Added
- Feature in progress

## [1.3.0] - 2026-05-26
### Added
- User authentication with JWT
- Password reset functionality

### Changed
- Improved error messages

### Fixed
- Login race condition

### Breaking Changes
- Removed session-based auth

## [1.2.0] - 2026-04-15
### Added
- User profiles
- Avatar upload

## [1.1.0] - 2026-03-01
### Added
- Initial release
```

---

## Git Workflow Checklist

**Before starting work:**
- [ ] Pull latest changes from main/develop
- [ ] Create feature branch with descriptive name
- [ ] Understand the task/issue

**During development:**
- [ ] Commit frequently (every 5-10 minutes)
- [ ] Write descriptive commit messages
- [ ] Keep commits atomic (one change per commit)
- [ ] Push to remote regularly

**Before creating PR:**
- [ ] Update branch with latest main/develop
- [ ] Run all tests locally
- [ ] Self-review your changes
- [ ] Write PR description

**During code review:**
- [ ] Address all review comments
- [ ] Re-request review after changes
- [ ] Keep PR updated with main/develop

**After merge:**
- [ ] Delete feature branch
- [ ] Verify deployment
- [ ] Monitor for issues

---

## Common Git Commands

### Branch Management

```bash
# List branches
git branch                    # Local branches
git branch -r                 # Remote branches
git branch -a                 # All branches

# Create branch
git checkout -b feature/name  # Create and switch
git branch feature/name       # Create only

# Switch branches
git checkout main
git switch main               # Modern alternative

# Delete branch
git branch -d feature/name    # Safe delete (merged only)
git branch -D feature/name    # Force delete
git push origin --delete feature/name  # Delete remote

# Rename branch
git branch -m old-name new-name
```

### Stashing Changes

```bash
# Save work in progress
git stash                     # Stash changes
git stash save "WIP: feature" # Stash with message

# List stashes
git stash list

# Apply stash
git stash apply               # Apply latest, keep stash
git stash pop                 # Apply latest, remove stash
git stash apply stash@{1}     # Apply specific stash

# Delete stash
git stash drop stash@{0}      # Delete specific
git stash clear               # Delete all
```

### Undoing Changes

```bash
# Discard local changes
git checkout -- file.ts       # Discard changes to file
git checkout .                # Discard all changes

# Unstage files
git reset HEAD file.ts        # Unstage file
git reset HEAD .              # Unstage all

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Revert commit (create new commit)
git revert <commit-hash>
```

### Viewing History

```bash
# View commit history
git log                       # Full history
git log --oneline             # Compact view
git log --graph --oneline     # Visual graph
git log -n 5                  # Last 5 commits

# View changes
git diff                      # Unstaged changes
git diff --staged             # Staged changes
git diff main..feature/name   # Compare branches

# View file history
git log -- file.ts            # Commits affecting file
git blame file.ts             # Line-by-line authorship
```

---

## Integration with Other Skills

**Use before git workflow:**
- `spec-driven-development-enhanced` - Define what to build
- `incremental-implementation` - Break into small commits

**Use during git workflow:**
- `tdd-iron-law` - Test before committing
- `github-code-review` - Review process

**Use after git workflow:**
- `github-pr-workflow` - PR creation and management

---

**Remember:** Good git workflow is about communication. Commits tell a story, branches organize work, and PRs facilitate collaboration. Keep it simple, keep it clean, keep it consistent.

---
> Source: [dinoudon/udon-collective-skills](https://github.com/dinoudon/udon-collective-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
