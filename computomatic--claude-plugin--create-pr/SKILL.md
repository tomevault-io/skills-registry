---
name: create-pr
description: Creates a GitHub PR for current work. Handles branch creation, committing, pushing, and PR creation.
metadata:
  author: computomatic
---

# Create Pull Request

Creates a complete PR from current work: branch, commit, push, and open the PR.

## Git State

Status:
!`git status`

Changes:
!`git diff`

Current branch:
!`git branch --show-current`

Recent commits:
!`if git rev-parse --verify HEAD >/dev/null 2>&1; then git log --oneline -5; else echo "(no commits yet)"; fi`

## Workflow

### Step 1: Check Branch State

Determine the current branch:
- If on `main` or `master`, create a new feature branch
- If already on a feature branch, continue on that branch

**Branch naming:** Generate from conversation context using format `{category}/{short-description}`:
- `feat/add-user-auth`
- `fix/login-validation`
- `refactor/extract-utils`

### Step 2: Identify Changes to Include

Review the conversation context and git diff:
- If an argument was provided, use it to determine which changes are in scope
- If no argument, infer scope from the conversation context
- Only stage files related to the intended PR scope
- Unrelated changes should NOT be staged

If uncertain about which files belong, ask the user.

### Step 3: Stage and Commit

1. Stage only the relevant files with `git add`
2. Write a clear, concise commit message
3. Commit the changes

### Step 4: Push to Remote

Push the branch to origin:
```
git push -u origin <branch-name>
```

### Step 5: Check for PR Template

Search for a PR template in the repository. Check these locations in priority order (filenames are case-insensitive):

1. `.github/pull_request_template.md`
2. `.github/PULL_REQUEST_TEMPLATE/` (directory with multiple templates)
3. `docs/pull_request_template.md`
4. `pull_request_template.md` (repository root)

Use `Glob` to find matching files and `Read` to read the template content.

If `.github/PULL_REQUEST_TEMPLATE/` contains multiple templates, pick the one most relevant to the changes (e.g., a bug fix template for fixes, a feature template for new features). If unsure which template fits, ask the user.

### Step 6: Create the PR

Create the PR using `gh pr create`:
- Title: Clear, imperative summary
- If argument was provided, use it to inform the description

**If a PR template was found:** Use the template's structure for the body, filling in each section based on the conversation context and the changes being submitted.

**If no PR template was found:** Write a plain text body of 1-2 paragraphs describing what and why. No headers, no "Test Plan" section, no markdown formatting in the body.

```
gh pr create --title "..." --body "..."
```

## Guidelines

- When a PR template is found, respect its structure and fill in all sections
- When no template is found, keep descriptions as plain prose (1-2 paragraphs) with no headers or sections
- Focus on what changed and why, not how
- If multiple unrelated changes exist, only include those relevant to the conversation or argument
- Always push before creating the PR
- Never add a signature line like "Generated with Claude Code" or similar to the PR description

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/computomatic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
