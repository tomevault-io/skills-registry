---
name: push-and-create-pr
description: Handles staging local changes, committing, pushing to a remote branch, and creating a GitHub pull request from the current project state.
metadata:
  author: rubendguez
---

# Skill: Push Changes and Create Pull Request

Use this skill **only when the working tree contains changes** and the intent is to push those changes and open a pull request on GitHub.

This skill assumes:
- A git repository already exists
- A remote `origin` is configured
- The user wants a clean, review-ready pull request

## Entry Conditions

Invoke this skill when:
- `git status` shows modified, added, or deleted files
- The user asks to push changes, open a PR, or "create a pull request"
- The task implies code review or merging into a base branch

Do NOT use this skill for:
- Repository initialization
- Complex rebases or conflict resolution
- Multi-branch release orchestration

---

## Execution Flow

### 1) Inspect Working Tree
- Run `git status --short`
- If no changes are present, stop and report that no action is required
- If changes exist, proceed

### 2) Determine Branch Context
- Identify current branch
- If on a protected branch (e.g. `main`, `master`, `develop`):
  - Create a new feature branch using a descriptive name derived from changes
  - Checkout the new branch
- If already on a non-protected branch, continue

### 3) Stage Changes
- Stage all relevant files using `git add .`
- If large or unrelated changes are detected, suggest splitting commits

### 4) Commit Changes
- Generate a concise, descriptive commit message (less than 50 characters when possible)
- Follow conventional commit style:
  - `feat:` for new features
  - `fix:` for bug fixes
  - `docs:` for documentation changes
  - `refactor:` for code refactoring
  - `test:` for test additions/changes
  - `chore:` for maintenance tasks
- Use commit body only if absolutely required for additional context
- Create the commit using `git commit -m 'message'`

### 5) Push Branch
- Push the current branch to `origin` using `git push`
- Set upstream tracking if not already configured

### 6) Create Pull Request Using GitHub CLI
**REQUIRED**: Use `gh pr create` with the following parameters:
- `--title`: Derived from commit message or change summary
- `--body`: Include:
  - Summary of changes
  - Reason for change
  - Testing performed (if detectable)
- `--base main`: Target the main branch
- `--label`: Add appropriate label(s):
  - `bug` for bug fixes
  - `enhancement` for improvements
  - `feature` for new features
  - `bugfix` for bug repairs
  - `workflow` for workflow/CI changes
  - `documentation` for docs changes

**Example command:**
```bash
gh pr create --title "feat: add user validation" --body "Added input validation for user registration form" --base main --label feature
```

### 7) Final Output
- Confirm:
  - Branch name
  - Commit hash
  - Pull request URL
- Provide a brief summary of actions taken

### 8) Merge Pull Request Using GitHub CLI
**When user requests to merge a PR:**
- Use `gh pr merge <number> --merge` command
  - **IMPORTANT**: Always use `--merge` flag (creates merge commit)
  - Do NOT use `--squash` or `--rebase` unless explicitly requested
- After successful merge, **ALWAYS** run post-merge cleanup

### 9) After Merging PR (Post-Merge Cleanup)
**IMPORTANT**: When a pull request is merged:
- **ALWAYS** run `git checkout main && git pull` to:
  - Switch back to the main branch
  - Pull the latest merged changes from remote
  - Ensure local main branch is up-to-date
- This prevents working on stale branches
- Keeps the local repository synchronized with remote

**Example workflow:**
```bash
gh pr merge 7 --merge
git checkout main && git pull
```

---

## Expected Behavior Examples

### Example 1
**User prompt:**  
“Push my changes and open a PR.”

**Actions:**  
1) Detect modified files  
2) Create feature branch if needed  
3) Commit changes  
4) Push to origin  
5) Open PR against `main`

---

### Example 2
**User prompt:**  
“I’m done with this feature, create a pull request.”

**Actions:**  
1) Stage all changes  
2) Commit with feature-appropriate message  
3) Push branch  
4) Generate PR with summary and test notes

---

## Failure Handling

- If push fails → explain cause and corrective action
- If PR creation fails → provide CLI output and next steps
- If authentication is missing → instruct how to authenticate with GitHub CLI

---

## Constraints

- Do not modify commit history unless explicitly requested
- Do not rebase or squash unless instructed
- **MUST use GitHub CLI (`gh pr create`) for creating pull requests**
- **MUST use GitHub CLI (`gh pr merge`) for merging pull requests**
- Always include appropriate labels when creating PRs

This skill is optimized for **fast, safe PR creation and merging from local changes** using GitHub CLI exclusively.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubendguez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
