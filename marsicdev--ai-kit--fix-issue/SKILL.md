---
name: fix-issue
description: Analyze and fix GitHub issues. Use when given a GitHub issue URL or number to investigate, understand the root cause, implement a fix, and verify the solution works. Use when this capability is needed.
metadata:
  author: marsicdev
---

# GitHub Issue Fixer

Analyze and implement a fix for: **$ARGUMENTS**

## Process

### Step 1: Read the Issue

- Fetch the issue from GitHub (use `gh issue view` or WebFetch)
- Understand the issue description
- Note reproduction steps
- Identify expected vs actual behavior

### Step 2: Locate Relevant Code

- Search the codebase for related files
- Identify the affected components, screens, or services
- Map out the code flow that's causing the issue

### Step 3: Analyze Root Cause

- Understand why the bug occurs or what's missing
- Trace the execution path
- Identify the exact location of the problem
- Consider related edge cases

### Step 4: Implement Fix

- Make the necessary code changes
- Follow existing code patterns and conventions
- Keep changes minimal and focused on the issue
- Add or update tests if applicable

### Step 5: Verify

- Ensure the fix addresses the issue
- Check for regressions
- Test edge cases
- Run existing tests

## Output Summary

After fixing, provide:

1. **Issue Summary**: Brief description of the reported problem
2. **Root Cause Analysis**: What was causing the issue
3. **Code Changes Made**: Files modified and what was changed
4. **Verification Steps**: How to confirm the fix works

## Constraints

- Follow existing code patterns and conventions
- Keep changes minimal and focused
- Add tests for the fix if the project has test coverage
- Use conventional commits when committing the fix
- Don't introduce breaking changes unless necessary

## Commit Message

When ready to commit, use format:
```
fix(scope): TICKET-123 brief description of the fix

Closes #123
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marsicdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
