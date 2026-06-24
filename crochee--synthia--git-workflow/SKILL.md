---
name: git-workflow
description: Use when branching, committing, resolving merge conflicts, or following collaborative git conventions
metadata:
  author: crochee
---

# Git Workflow Skill

You are an expert in Git workflows and collaborative version control. Follow these conventions.

## Branching Strategy

### Main Branches
- `main` or `master`: Production-ready code
- `develop`: Integration branch for features

### Feature Branches
```
feature/<ticket-id>-short-description
fix/<ticket-id>-short-description
hotfix/<ticket-id>-short-description
```

### Branch Naming Examples
```
feature/ABC-123-user-authentication
fix/DEF-456-login-bug
hotfix/GHI-789-security-patch
```

## Commit Conventions

### Commit Message Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting (no code change)
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance tasks

### Examples
```
feat(auth): add OAuth2 login support

Implemented Google and GitHub OAuth2 authentication.
- Added auth provider configuration
- Updated user model with oauth fields
- Created login flow handlers

Closes ABC-123
```

## Merging Strategies

### Merge vs Rebase
- **Merge**: Preserves history, use for shared branches
- **Rebase**: Clean linear history, use for local cleanup before merge

### Pull Request Workflow
1. Create feature branch from `develop`
2. Make commits with clear messages
3. Push and open PR
4. Request reviews
5. Address feedback
6. Squash and merge

## Conflict Resolution

### Before Resolving
```bash
git fetch origin
git checkout feature-branch
git merge origin/develop
```

### Resolving Conflicts
1. Identify conflicting files
2. Review both versions
3. Choose or combine changes
4. Mark as resolved: `git add <file>`
5. Complete merge: `git commit`

### Best Practices
- Communicate with team members
- Take context into account
- Test after resolution
- Don't rush - understand both sides

## Useful Commands

### Stash Changes
```bash
git stash
git stash pop
git stash list
```

### Interactive Rebase
```bash
git rebase -i HEAD~3
```

### Bisect for Bugs
```bash
git bisect start
git bisect bad
git bisect good <commit>
```

## Hooks

### Pre-commit
- Run linters
- Check code formatting
- Run fast tests

### Commit-msg
- Validate commit message format
- Check ticket ID presence

---
> Source: [crochee/synthia](https://github.com/crochee/synthia) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
