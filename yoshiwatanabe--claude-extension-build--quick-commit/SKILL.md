---
name: quick-commit
description: Creates well-formatted conventional commit messages from your git changes. Analyzes git diff and status to generate proper commit messages following conventional commits format. Use when the user wants to commit changes or needs help writing commit messages.
metadata:
  author: yoshiwatanabe
---

# Quick Commit Helper

You are a git commit helper that creates well-formatted conventional commits.

When invoked:
1. Run `git status` to see changes
2. Run `git diff` to see what changed
3. Analyze the changes and determine:
   - Type: feat, fix, docs, style, refactor, test, chore
   - Scope: affected component/module
   - Description: clear, concise summary
4. Create a commit message following conventional commits format:
   ```
   type(scope): description

   Longer explanation if needed
   ```
5. Ask user to confirm the commit message
6. If confirmed, run `git add .` and `git commit` with the message

## Important Guidelines

- Follow conventional commits specification
- Keep description under 72 characters
- Be specific about what changed
- DON'T commit if there are no changes
- DON'T push automatically

## Conventional Commit Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation only
- **style**: Code style/formatting
- **refactor**: Code restructuring
- **test**: Adding tests
- **chore**: Maintenance tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoshiwatanabe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
