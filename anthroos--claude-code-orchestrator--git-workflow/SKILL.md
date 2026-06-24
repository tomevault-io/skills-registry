---
name: git-workflow
description: Git workflow — branch, PR, squash merge, cleanup Use when this capability is needed.
metadata:
  author: anthroos
---

# Git Workflow

> Standardized git workflow: branch → PR → squash merge → delete branch. Never push to main.

## When to use

- "Create a branch for this feature"
- "Open a PR"
- "Merge and clean up"
- Any git operation that should follow team conventions

## How to execute

### Step 1: Create branch

```bash
# Branch naming: {type}/{short-description}
# Types: feature, fix, refactor, docs, chore
git checkout -b feature/add-user-auth
```

### Step 2: Commit changes

```bash
# Commit message format: "<Action>: <what>"
# Actions: Add, Update, Fix, Remove, Refactor
git add <specific-files>
git commit -m "Add: user authentication flow"
```

### Step 3: Push and create PR

```bash
git push -u origin feature/add-user-auth
gh pr create --title "Add user authentication" --body "## Summary
- Added login/logout flow
- Added session management

## Test plan
- [ ] Login with valid credentials
- [ ] Login with invalid credentials
- [ ] Session expires after timeout"
```

### Step 4: After review — squash merge

```bash
gh pr merge --squash --delete-branch
```

### Step 5: Clean up local

```bash
git checkout main
git pull
git branch -d feature/add-user-auth
```

## Rules

- **Never push directly to main** — always use a branch + PR
- **Squash merge** — keeps history clean
- **Delete branch after merge** — no stale branches
- **One PR per logical change** — don't mix unrelated changes

## Troubleshooting

| Problem | Solution |
|---------|----------|
| PR has conflicts | `git checkout main && git pull && git checkout feature-branch && git rebase main` |
| Accidentally committed to main | `git reset HEAD~1` then create branch |
| Need to update PR | Push to the same branch, PR updates automatically |

## Related skills

- `code-review` — review before merging

---
> Source: [anthroos/claude-code-orchestrator](https://github.com/anthroos/claude-code-orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
