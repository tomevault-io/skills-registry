---
name: flutter-test-focus
description: Fast local test workflow that prioritizes changed tests first, then optional full-suite execution for daily feedback loops. Use when this capability is needed.
metadata:
  author: nickcuypers
---

## When to use / when NOT to use
- Use during active feature work for quick feedback.
- Do not use as your only CI gate for release PRs.

## Preconditions (tools, versions, repo state)
- Flutter installed.
- Test directory exists.

## Workflow (DISCOVER → PLAN → EXECUTE → VERIFY → REPORT)
1. DISCOVER: detect changed files and test availability.
2. PLAN: decide targeted tests to run.
3. EXECUTE: run changed tests; optionally run full suite.
4. VERIFY: capture completion and key failures.
5. REPORT: summarize targeted/full results.

## Exact commands and expected signals
```bash
skills/flutter-test-focus/scripts/run.sh --dry-run
skills/flutter-test-focus/scripts/run.sh --verify-only
skills/flutter-test-focus/scripts/run.sh --execute
```
Success: targeted tests discovered/executed and report produced.
Failure: missing Flutter toolchain or no test setup.

## If it fails (checklist)
- Ensure `test/` exists.
- Validate command in local Flutter environment.
- Run full `flutter test` if diff-based detection is empty.

## Final report template
- Changed test files detected.
- Targeted run status.
- Full suite status (if run).
- Next actions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickcuypers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
