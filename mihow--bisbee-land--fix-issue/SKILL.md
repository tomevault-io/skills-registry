---
name: fix-issue
description: Fix a GitHub issue from start to PR Use when this capability is needed.
metadata:
  author: mihow
---

Fix GitHub issue: $ARGUMENTS

## Workflow

1. **Understand**: Use `gh issue view $ARGUMENTS` to get details
2. **Investigate**: Search codebase for relevant files
3. **Plan**: Identify changes needed
4. **Implement**: Make the changes
5. **Test**: Write/run tests to verify the fix
6. **VERIFY** (Required):
   ```bash
   # Must pass ALL of these before continuing:
   python -c "from my_project import *; print('imports ok')"
   pytest -x
   pytest tests/test_smoke.py -v  # Smoke tests
   my-project info  # CLI actually runs
   ```
7. **Lint**: Run linter and type checker
8. **Commit**: Create descriptive commit message
9. **PR**: Push and create pull request with `gh pr create`

## IMPORTANT: Verification

Do NOT proceed to commit/PR until you have:
- [ ] Run the code you changed (not just tests)
- [ ] Verified output is correct
- [ ] Checked for errors/warnings in output

Tests passing is necessary but NOT sufficient. Actually run the code.

## Guidelines

- Address root cause, not just symptoms
- Follow existing code patterns
- Include test coverage for the fix

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mihow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
