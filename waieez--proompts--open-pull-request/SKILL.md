---
name: open-pull-request
description: > Use when this capability is needed.
metadata:
  author: waieez
---

# Open Pull Request

Turn a verified feature branch into a pull request that a reviewer can evaluate without chasing context.

## Procedure

1. **Confirm readiness.** Verify the branch passes all checks. Do not open a pull request with known failures.

2. **Rebase if needed.** Ensure the branch is current with the default branch. Resolve conflicts before opening.

3. **Write the title.** It should complete: "This PR will ___." Follow the repository's conventions for format.

4. **Write the description.** Include:
   - **What** — a concise summary of the changes.
   - **Why** — link to the issue and explain the motivation.
   - **How** — describe the approach at the level a reviewer needs.
   - **Verification** — what evidence proves the changes work (test results, before/after, commands to run).

5. **Link the issue.** Use closing keywords (e.g., "Closes #42") so the issue resolves on merge.

6. **Review your own diff.** Read the pull request as a reviewer would. Check for:
   - Unintended changes (stray formatting, debug statements, unrelated refactors).
   - Missing context (would a reviewer need to ask "why?" at any line?).
   - Incomplete work (TODOs left in code, commented-out blocks, placeholder values).
   - Test coverage gaps (are the acceptance criteria all tested?).

7. **Submit the pull request.** Request review from the appropriate people or teams.

## When Steps Fail

- **Checks fail on the branch.** Do not open the PR. Fix the failures first.
- **Rebase produces conflicts you cannot resolve confidently.** Ask for help from the author of the conflicting changes rather than guessing at the resolution.
- **No obvious reviewer.** Check CODEOWNERS, recent git blame for the touched files, or the repository's contributing guide for review assignment guidance.

## Examples

### Example 1: Feature implementation PR

**Title:** Add --timeout flag to CLI

**Description:**

> ## What
> Adds a `--timeout` flag to the CLI with a default of 30 seconds. The value is threaded through to the HTTP client.
>
> ## Why
> Closes #58. Users on slow networks need to increase the default timeout. Currently there is no way to configure this.
>
> ## How
> - Added `--timeout` argument to the CLI parser in `src/cli.py`.
> - Passed the value to `client.request(timeout=...)` in `src/client.py`.
> - Default remains 30s to preserve existing behavior.
>
> ## Verification
> - `make check` passes (tests, lint, type-check).
> - New tests: flag parsing, default value, client integration.
> - Manual test: `cli --timeout 60 fetch` completes with extended timeout.

### Example 2: Bug fix PR

**Title:** Fix 500 error on Unicode CSV upload

**Description:**

> ## What
> Changes the CSV upload handler encoding from ASCII to UTF-8.
>
> ## Why
> Closes #73. Users uploading CSVs with non-ASCII characters hit a 500 error because the handler decoded with `ascii`.
>
> ## How
> - Changed encoding in `src/api/upload.py` from `ascii` to `utf-8`.
> - Added encoding detection as a fallback for ambiguous files.
>
> ## Verification
> - `pytest` passes — includes new regression test with Japanese characters.
> - Tested manually with a CSV containing Chinese, Arabic, and emoji characters.

## Troubleshooting

**Description feels too long.**
Cause: Too much implementation detail in the "How" section.
Solution: The description should explain the approach, not narrate the diff. A reviewer will read the code — the description provides the map, not the territory.

**Cannot find the right closing keyword syntax.**
Cause: Different platforms use different syntax.
Solution: GitHub supports: `Closes #N`, `Fixes #N`, `Resolves #N`. Place these in the description body, not the title.

**Self-review reveals problems.**
Cause: Step 6 surfaced issues — this is working as intended.
Solution: Fix the issues before submitting. A clean self-review is faster than a review round-trip.

## Verification

The pull request description is self-contained — a reviewer can understand the what, why, and how without leaving the page. The issue is linked. The branch is clean and passing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waieez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
