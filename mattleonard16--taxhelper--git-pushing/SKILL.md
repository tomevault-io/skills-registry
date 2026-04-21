---
name: git-pushing
description: Stage, commit, and push git changes with conventional commit messages. Use when user wants to commit and push changes, mentions pushing to remote, or asks to save and push their work. Also activates when user says "push changes", "commit and push", "push this", "push to github", or similar git workflow requests. Use when this capability is needed.
metadata:
  author: mattleonard16
---

# Git Push Workflow

Stage all changes, create a conventional commit, and push to the remote branch.

## When to Use

Automatically activate when the user:
- Explicitly asks to push changes ("push this", "commit and push")
- Mentions saving work to remote ("save to github", "push to remote")
- Completes a feature and wants to share it
- Says phrases like "let's push this up" or "commit these changes"

## Workflow

### 1. Check Status
```bash
git status
```

### 2. Stage Changes
```bash
git add -A
```

### 3. Create Conventional Commit

Use conventional commit format:
- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `chore:` - Maintenance tasks
- `refactor:` - Code refactoring
- `test:` - Adding/fixing tests
- `style:` - Formatting changes

Example:
```bash
git commit -m "feat: add user authentication flow"
```

### 4. Push to Remote
```bash
git push -u origin main
```

Or for current branch:
```bash
git push
```

## Best Practices

- Review changes before committing: `git diff`
- Keep commits focused and atomic
- Write descriptive commit messages
- Use branch names that describe the work
- Push frequently to avoid large divergences

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattleonard16) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
