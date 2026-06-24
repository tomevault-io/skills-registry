---
name: implement
description: > Use when this capability is needed.
metadata:
  author: waieez
---

# Implement

Turn a GitHub issue into verified code on a feature branch.

## Procedure

1. **Read the issue.** Understand the objective, acceptance criteria, and scope boundaries. If anything is ambiguous, comment on the issue and wait for clarification — do not begin implementation until the question is answered.

2. **Check contribution standards.** Look for the repository's contributing guide, commit conventions, and branch naming rules. Follow them. If the repository doesn't define its own, follow [ours](../../CONTRIBUTING.md).

3. **Create a feature branch.** Name it to reflect the issue (e.g., `42-add-retry-logic`).

4. **Trace the affected code.** Read the paths the change touches. Confirm your understanding before editing.

5. **Implement the change.** Every change should have a clear purpose traceable to the acceptance criteria.

6. **Write or update tests.** Tests should verify the acceptance criteria directly.

7. **Verify and commit.** Run the project's checks (tests, linter, build). If any fail, fix the failure before committing. Commit following the repository's standards.

8. **Confirm acceptance criteria.** Walk through each criterion and confirm it is met with objective evidence.

## When Steps Fail

- **Tests fail after implementation.** Read the failure output. Fix the root cause — do not skip or disable tests. If the failure is unrelated to your change, document it in the PR description and flag it for the reviewer.
- **Checks fail on commit.** Identify which check failed (lint, type-check, build). Fix the violation. Do not commit with known failures.
- **Branch conflicts with the default branch.** Rebase onto the default branch and resolve conflicts. Re-run all checks after resolving.
- **Acceptance criteria are unverifiable with the current codebase.** Comment on the issue explaining what's missing (e.g., no test infrastructure, missing fixture data). Propose a path forward rather than guessing.
- **Branch is in a bad state.** If iterating has introduced confusion, reset to the last clean commit (`git reset --hard <commit>`) and re-implement from that point. A clean restart is faster than debugging tangled changes.

## Examples

### Example 1: Adding a configuration option

**Issue:** "Add a `--timeout` flag to the CLI with a default of 30 seconds."

**Steps taken:**
1. Read the issue — acceptance criteria: flag exists, default is 30s, value is passed to the HTTP client.
2. Check CONTRIBUTING.md — project uses conventional commits, branch naming is `<issue>-<slug>`.
3. Create branch `58-add-timeout-flag`.
4. Trace code — CLI argument parsing in `src/cli.py`, HTTP client in `src/client.py`.
5. Add `--timeout` argument with default 30, thread the value to `client.request(timeout=...)`.
6. Add tests: flag parsing test, default value test, client receives timeout test.
7. Run `make check` — all pass. Commit: `feat(cli): Add --timeout flag with 30s default`.
8. Walk acceptance criteria — flag exists, default is 30s, value reaches HTTP client. All met.

### Example 2: Fixing a bug with a regression test

**Issue:** "CSV upload returns 500 on Unicode input."

**Steps taken:**
1. Read issue — acceptance criteria: Unicode CSV returns 200, data preserved, regression test added.
2. Contribution standards — conventional commits, branch format `<issue>-<slug>`.
3. Create branch `73-fix-unicode-csv-upload`.
4. Trace code — upload handler in `src/api/upload.py`, encoding at line 42 uses `ascii`.
5. Change encoding to `utf-8`. Add input validation for encoding detection.
6. Add test: upload a CSV with Japanese characters, assert 200 and correct data.
7. Run `pytest && ruff check` — all pass. Commit: `fix(api): Handle Unicode in CSV upload`.
8. Acceptance criteria — 200 response, data preserved, regression test. All met.

## Troubleshooting

**Issue acceptance criteria are ambiguous.**
Cause: The issue was not triaged thoroughly.
Solution: Comment on the issue with specific questions. Do not start implementation until the criteria are clear.

**Unfamiliar codebase or domain.**
Cause: The change touches code you haven't read before.
Solution: Spend time tracing before editing. Read tests for the module — they document expected behavior. If the codebase has architecture docs, read them first.

**Tests pass locally but fail in CI.**
Cause: Environment differences (OS, dependency versions, database state).
Solution: Check the CI logs for the specific failure. Reproduce locally by matching the CI environment as closely as possible. Do not push workarounds without understanding the root cause.

## Verification

All acceptance criteria from the issue are met. Tests pass. The branch is clean and ready for a pull request.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waieez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
