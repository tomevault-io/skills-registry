---
name: conventional-commit
description: Create a git commit for the current changes, following The Conventional Commits. Use when this capability is needed.
metadata:
  author: nsfisis
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD --`
- Recent commits: !`git log --oneline -5`

## Instructions

- Do NOT use `git -C` flag. Always run git commands from the current working directory.

1. Check the git status and changes.
2. Analyze the changes and draft a commit message following Conventional Commits format:
   - Types: feat, fix, refactor, test, docs, chore, style, perf, ci, build
       - **refactor**: Only for internal code improvements that do NOT change externally observable behavior. If the change alters config, adds functionality, fixes a bug, or changes any visible output, it is NOT a refactor. When in doubt, prefer feat, fix, chore, or another more accurate type.
   - Format: `<type>(<scope>): <description>`
   - Keep the first line under 72 characters
   - Add a blank line and body if more context is needed
3. Create the commit with the message (`git commit -a -m "..."`)

## Commit Message Format

```
<type>(<scope>): <short description>

<optional body explaining the "why" not the "what">
```

## Examples

- `feat(auth): add refresh token rotation`
- `fix(sync): handle network timeout during push`
- `refactor(db): extract repository pattern for cards`
- `test(api): add integration tests for deck endpoints`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsfisis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
