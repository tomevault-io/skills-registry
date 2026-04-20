---
name: implement-feature-safely
description: Use when implementing a new feature end-to-end with tests, flags, and minimal regressions.
metadata:
  author: juantaco4you
---

## Goal
Implement the feature with correct behavior, test coverage, and a clean diff that is easy to review.

## Rules
- Prefer small, composable changes over sweeping rewrites.
- No silent behavior changes outside the requested scope.
- Add tests that would have caught the bug/absence of feature.

## Workflow
1. Locate the correct insertion points (entry points, handlers, services).
2. Add a feature flag/config toggle if risk is non-trivial or rollout is needed.
3. Implement the smallest viable slice first:
   - Plumb inputs
   - Produce correct outputs
   - Handle errors explicitly
4. Add tests in this order:
   - Unit tests for core logic
   - Integration tests for the feature boundary
   - Golden snapshot tests only if appropriate
5. Run the project’s standard checks (tests, lint, typecheck, build).
6. Clean up:
   - Remove dead code
   - Improve naming
   - Ensure logging is useful and not noisy
7. Summarize:
   - What changed
   - Why it is safe
   - What to watch in production

## Output format
- Summary of implementation
- Files changed (with intent)
- Tests added/updated
- Verification commands run
- Risks/rollout notes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/juantaco4you) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
