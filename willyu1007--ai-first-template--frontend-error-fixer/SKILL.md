---
name: frontend-error-fixer
description: Workflow for diagnosing and fixing frontend build/runtime errors with minimal, targeted changes. Keywords: error fixing, typescript errors, build errors, runtime errors, debugging. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Frontend Error Fixer

This workflow diagnoses and fixes frontend errors (build-time or runtime) with minimal, targeted changes.

---

## Purpose & Scope

Use this workflow when you see:
- TypeScript compile errors
- Lint errors (if applicable)
- Browser runtime errors (console, error boundaries)
- Network/API integration errors surfaced in the UI

Out of scope:
- Large refactors (use refactoring workflows)

---

## Inputs & Preconditions

Inputs:
- Full error message + stack trace
- File/line locations (if available)
- Steps to reproduce (if runtime)

Preconditions:
- Confirm the error is reproducible.
- Identify whether it is build-time or runtime.

---

## Steps

1. **Classify the error**
   - Build-time (typecheck/bundle/lint) vs runtime (browser) vs network (API/CORS).
2. **Find the root location**
   - Trace to the first meaningful stack frame in the app code.
3. **Identify the minimal fix**
   - Fix the root cause (typing, nullability, missing import, hook rule violation).
4. **Apply the change**
   - Keep diffs small and localized.
5. **Verify**
   - Re-run the build/typecheck.
   - Re-test the UI flow that triggered the issue.
6. **Harden (only if justified)**
   - Add defensive guards only where the error occurs (avoid broad "optional chaining everywhere").

---

## Outputs

- Fixed code change (minimal scope)
- Verification notes (what command/run confirmed the fix)

---

## Safety Notes

- Avoid "fixing" by weakening types (`any`, `@ts-ignore`) unless explicitly approved.
- Preserve existing behavior; do not refactor unrelated areas.

---

## Related Skills

- `frontend-dev-guidelines`
- `error-tracking` (in repo/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
