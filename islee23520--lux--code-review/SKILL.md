---
name: code-review
description: Lazy-load only when this workflow is explicitly needed. Use to review LUX diffs for correctness, maintainability, security, and tests. Check Rust Axum/CLI conventions, React strict hooks, no UI mock fallback data, Unity 6000+ bridge compatibility, protocol consistency, and fresh verification evidence. Use when this capability is needed.
metadata:
  author: islee23520
---

# Code Review

Use to review LUX diffs for correctness, maintainability, security, and tests. Check Rust Axum/CLI conventions, React strict hooks, no UI mock fallback data, Unity 6000+ bridge compatibility, protocol consistency, and fresh verification evidence.

## Passive Loading Rule

Do not preload this skill during startup, hot reload, or background indexing. Read it only when the user request directly matches this workflow.

## Minimal Procedure

1. Confirm the target result and affected LUX subsystem.
2. Read only the files or evidence needed for the decision.
3. Apply the checklist above without expanding scope.
4. Produce concise output with evidence, risks, and the next verified action.

## Output

- Result or verdict.
- Evidence used.
- Risks or blockers.
- Verification performed or the smallest remaining check.

---
> Source: [islee23520/lux](https://github.com/islee23520/lux) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
