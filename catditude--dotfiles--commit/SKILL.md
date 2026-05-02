---
name: commit
description: Create git commits with conventional commit format and push. Use when the user invokes /commit. Stages files, generates commit messages, and pushes to remote. Use when this capability is needed.
metadata:
  author: catditude
---

# Commit

Stage, commit, and push changes.

## Workflow

1. **Gather context** (run in parallel):
   - `git status` — check for changes; if none, inform user and stop
   - `git diff` (staged + unstaged) — review changes for commit message
   - `git log --oneline -5` — match recent commit style

2. **Stage and commit**:
   - Stage specific files related to the changes (avoid `git add -A` unless user requests it)
   - Generate commit message using conventional commits format
   - Use HEREDOC for commit message to preserve formatting

3. **Push**:
   - Push to remote after successful commit

## Commit Message Convention

```
<type>: <short description>

<optional body>
```

**Example:**
```
feat: add user authentication flow

Implement OAuth2 login with Google and GitHub providers.
```

## Edge Cases

- **Pre-commit hook failure**: Fix the issue, re-stage, create a NEW commit (never amend)
- **Nothing to commit**: Inform the user and stop
- **Secrets detected** (.env, credentials.json, etc.): Warn and exclude from staging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/catditude) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
