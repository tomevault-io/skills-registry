---
name: fix-issue
description: description: Views and fixes a GitHub issue. Use when asked to fix or work on an issue. Use when this capability is needed.
metadata:
  author: remrama
---
---
name: fix-issue
description: Views and fixes a GitHub issue. Use when asked to fix or work on an issue.
skills:
  - run-tests
---

Please analyze and fix the GitHub issue: $ARGUMENTS.

Follow these steps:

1. Use `gh issue view` to get the issue details
2. Understand the problem described in the issue
3. Search the codebase for relevant files
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message
8. Push and create a PR

Remember to use the GitHub CLI (`gh`) for all GitHub-related tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/remrama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
