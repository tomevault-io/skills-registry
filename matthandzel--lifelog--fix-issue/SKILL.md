---
name: fix-issue
description: Fix a GitHub issue end-to-end with tests and validation Use when this capability is needed.
metadata:
  author: matthandzel
---
Analyze and fix the GitHub issue: $ARGUMENTS.

1. Use `gh issue view $ARGUMENTS` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files using Grep and Glob
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix: `just test`
6. Run full validation: `just validate`
7. Create a descriptive commit: `type: short description`
8. Push and create a PR with `gh pr create`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matthandzel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
