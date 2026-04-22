---
name: manage-git-flow
description: Standard procedure for git operations, conventional commits, and branching. Use when this capability is needed.
metadata:
  author: row0902
---

# 🌿 Git Flow Management Skill

## Context
Maintain a clean history and strict semantic versioning practices.

## 1. Starting a Task (Feature Branch)

```powershell
# Format: type/short-description
git checkout -b feat/add-login-ui
# OR
git checkout -b fix/auth-timeout
```

## 2. Work & Commit (Conventional Commits)

Format: `<type>(<scope>): <description>`

*   `feat(ui): add new login panel`
*   `fix(auth): resolve token refresh timeout`
*   `refactor(core): split large config file`
*   `docs(readme): update installation steps`
*   `chore(deps): upgrade wxpython`

**Rule:** Small, atomic commits.

## 3. Finishing a Task

1.  **Verify:** Run `uv run pytest`.
2.  **Lint:** Run `uv run ruff check`.
3.  **Merge:**
    *   (Agent typically works on current branch, but if simulating merge):
    ```powershell
    git checkout main
    git merge --squash feat/add-login-ui
    git commit -m "feat(ui): add login panel implementation"
    git branch -D feat/add-login-ui
    ```

## Checklist
*   [ ] Did I run `pre-commit` checks manualy or verify they would pass?
*   [ ] Is the commit message semantic?
*   [ ] Did I delete valid code or just commented it out? (Delete it).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/row0902) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
