---
name: github-actions-failure-debugging
description: Debug guide for failing GitHub Actions workflows. Use when CI fails, tests fail on Actions, or logs show exit code != 0. Use when this capability is needed.
metadata:
  author: jtakumi
---

# Goals
- Identify the *first* failing step and the real root cause (not the cascade).
- Produce a minimal fix (workflow or code) and re-run checks.

# Procedure
1. Find the failing workflow run and the failing job/step. Extract:
   - runner OS, language/runtime versions, cache usage
   - exact command that failed
2. Classify failure:
   - build tool / dependency resolution
   - test failure
   - lint/static analysis
   - auth/permissions/secrets
3. Reproduce locally when possible:
   - use the same command as the workflow
   - if not possible, provide a deterministic reproduction plan.
4. Fix strategy:
   - prefer smallest change
   - avoid disabling checks unless justified
5. Verification:
   - ensure job matrix still passes
   - update docs if workflow usage changed

# Output format
- Root cause (1-2 sentences)
- Evidence (log excerpt references)
- Fix diff + why it works
- How to validate (exact commands)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jtakumi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
