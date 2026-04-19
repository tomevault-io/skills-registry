---
name: git-workflow
description: Use for all Git operations, version control workflows, and repository management. Handles commits, branches, merges, releases. Use when this capability is needed.
metadata:
  author: funkymonkeymonk
---

# Git Workflow Skill

## Core Operations
- Check status: `git status`
- Stage changes: `git add .` or specific files
- Commit changes: `git commit -m "message"`
- Push changes: `git push`
- Create branches: `git checkout -b feature-name`
- Merge branches: `git merge branch-name`

## Commit Workflow
When making commits:

1. **Check current state**: Run `git status` to see changes
2. **Review changes**: Use `git diff` to see what will be committed
3. **Stage changes**: Use `git add` to stage relevant files
4. **Create commit**: Write descriptive commit messages following project conventions
5. **Push changes**: Send to remote repository

## Branch Management

### Feature Branch Workflow
```bash
# Create new feature branch
git checkout -b feature/new-feature

# Work on feature...
git add .
git commit -m "Add new feature implementation"

# Push branch
git push -u origin feature/new-feature
```

### Merge Process
```bash
# Switch to main branch
git checkout main
git pull

# Merge feature branch
git merge feature/new-feature

# Push merged changes
git push

# Clean up feature branch (optional)
git branch -d feature/new-feature
```

## Release Workflow
1. Update version numbers if needed
2. Create release branch or tag
3. Update changelog
4. Create release commit
5. Tag release: `git tag v1.0.0`
6. Push tags: `git push --tags`

## Common Issues & Solutions

### Merge Conflicts
1. Identify conflicting files with `git status`
2. Open files and resolve conflicts
3. Mark as resolved: `git add <file>`
4. Complete merge: `git commit`

### Undo Operations
- Unstage file: `git reset HEAD <file>`
- Undo last commit: `git reset --soft HEAD~1`
- Undo commit completely: `git reset --hard HEAD~1`

## Repository Maintenance
- Clean up branches: `git branch -d branch-name`
- Prune remote branches: `git remote prune origin`
- Check history: `git log --oneline --graph`

## Integration with Other Skills
This skill often works with:
- **taskfile**: For automated git tasks
- **format**: For code formatting before commits
- **project-specific workflows**: For repository-specific processes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/funkymonkeymonk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
