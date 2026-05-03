---
name: fix-issue
description: Analyze and fix a GitHub issue end-to-end Use when this capability is needed.
metadata:
  author: smartwhale8
---

Analyze and fix the GitHub issue: $ARGUMENTS

Follow this workflow:

1. **Understand**: Run `gh issue view $ARGUMENTS` to get issue details
2. **Investigate**: Search the codebase for relevant files and understand the root cause
3. **Plan**: Describe your approach before making changes
4. **Implement**: Make the necessary code changes
5. **Test**: Write a test that would have caught this issue, then run the test suite
6. **Verify**: Ensure lint and type checks pass
7. **Commit**: Create a descriptive commit message referencing the issue
8. **PR**: Push and create a PR with `gh pr create`

Important:
- Address the ROOT CAUSE, not just the symptom
- If the fix requires changes across multiple files, explain why
- If the issue is unclear, ask for clarification before implementing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smartwhale8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
