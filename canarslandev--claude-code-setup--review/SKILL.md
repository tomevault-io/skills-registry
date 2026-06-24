---
name: review
description: Perform a thorough code review on specified files or changes Use when this capability is needed.
metadata:
  author: canarslandev
---

# Code Review Skill

Perform a comprehensive code review on the specified files, directory, or recent changes.

## Usage

```
/review                     # Review all uncommitted changes
/review src/api/            # Review all files in a directory
/review src/auth/login.ts   # Review a specific file
/review --last-commit       # Review the last commit
```

## Steps

1. **Determine what to review**
   - If a path is provided, review those files
   - If `--last-commit` flag, review `git diff HEAD~1`
   - If no arguments, review `git diff` (all uncommitted changes)

2. **Read and analyze the code**
   For each file, check:

   ### Correctness
   - Logic errors or bugs
   - Off-by-one errors
   - Null/undefined handling
   - Race conditions
   - Edge cases not handled

   ### Security
   - SQL injection vulnerabilities
   - XSS vulnerabilities
   - Hardcoded secrets or credentials
   - Improper input validation
   - Authentication/authorization issues

   ### Performance
   - N+1 query problems
   - Unnecessary re-renders (React)
   - Missing indexes (if DB queries visible)
   - Memory leaks
   - Expensive operations in loops

   ### Code Quality
   - Naming clarity
   - Function length and complexity
   - Code duplication
   - Proper error handling
   - Type safety (TypeScript)

   ### Testing
   - Are new features tested?
   - Are edge cases covered?
   - Are tests meaningful (not just checking happy path)?

3. **Generate the review**
   Output a structured review with:
   - Summary (1-2 sentences)
   - Findings grouped by severity:
     - **Critical** — must fix before merge
     - **Warning** — should fix, potential issues
     - **Suggestion** — nice to have improvements
     - **Praise** — good patterns worth noting
   - Each finding should include:
     - File and line reference
     - Description of the issue
     - Suggested fix (with code if applicable)

## Output Format

```markdown
## Code Review Summary
[Brief overview]

### Critical (X issues)
- **[file:line]** — [description]
  Suggested fix: [solution]

### Warnings (X issues)
- **[file:line]** — [description]

### Suggestions (X items)
- **[file:line]** — [description]

### Highlights
- **[file:line]** — [what's good about it]
```

## Rules

- Be constructive, not harsh
- Provide actionable suggestions with code examples
- Don't nitpick formatting if there's a formatter configured
- Focus on logic and architecture over style
- Always mention good patterns too — reviews shouldn't be only negative

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canarslandev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
