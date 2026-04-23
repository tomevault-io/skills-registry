---
name: fix-issue
description: Fetches a GitHub issue, implements the fix, and verifies it. Use when user wants to fix an issue, implement issue changes, or resolve a bug. Use when this capability is needed.
metadata:
  author: sasamuku
---

# Fix Issue

Fetch a GitHub issue, implement the fix, and verify it.

## Arguments

Issue number (e.g., `123` or `#123`)

$ARGUMENTS

## Steps

1. Extract the issue number from the arguments
2. Fetch issue details:
   ```bash
   gh issue view <issue-number>
   ```
3. Understand the problem described in the issue
4. Search the codebase for relevant files
5. Implement the necessary changes
6. Write and run tests to verify the fix
7. Run linting and type checking (if available in project)
8. Create a descriptive commit message following Conventional Commits

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sasamuku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
