---
name: commit
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# Commit Skill

Create a git commit with comprehensive pre-commit verification. Prevents buggy code from reaching git history.

## Workflow

Execute these steps **in order**, stopping if any blocking step fails:

### 1. Build Verification (BLOCKING)
- Run the project's build command (e.g., `npm run build`)
- If build fails, **STOP** and fix errors immediately
- DO NOT proceed to commit if build is broken

### 2. Type Check (BLOCKING)
- Run `npm run typecheck` or `npx tsc --noEmit`
- If type errors exist, **STOP** and fix them immediately

### 3. Test Execution (NON-BLOCKING)
- Check if test scripts exist in package.json
- If tests exist, run them (e.g., `npx vitest run`)
- If tests fail, **report but allow user to decide** whether to proceed
- If no tests exist, skip silently

### 4. Git Status Review
Run in parallel:
```bash
git status
git diff --staged
git diff
```
- Show what will be committed
- Identify any untracked files that should be staged

### 5. Stage Changes
- **Prefer staging specific files by name** rather than `git add -A`
- **Avoid staging:** `.env`, `credentials.*`, large binaries
- If sensitive files are detected, **warn and skip them**

### 6. Create Commit
- Analyze the staged changes thoroughly
- Write a **conventional commit message**:
  - Format: `<type>: <subject>` (e.g., `feat: add keyboard navigation`)
  - Types: `feat`, `fix`, `refactor`, `docs`, `test`, `style`, `chore`
  - Subject: concise, focus on **why** not what
- Include co-author line
- Use heredoc for commit message formatting

### 7. Post-Commit Verification
- Run `git status` and `git log -1 --oneline`
- Verify commit succeeded, report hash and message

## Important Rules

- **NEVER skip build or type check** — these are quality gates
- **NEVER use `--no-verify`** or bypass git hooks
- **NEVER ask clarifying questions** — make reasonable decisions and proceed
- If build or type check fails, fix immediately — do not defer to user

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
