---
name: visual-regression-guardian
description: Use when the request requires set up Playwright visual regression testing: baselines, multi-viewport, state coverage, CI integration.
metadata:
  author: ricardohg1998-create
---

# Visual Regression Guardian (Playwright)

## Do not use when
- The request is unrelated to this domain or requires a different specialized skill.
- The user asks only for high-level discussion without applying this workflow.
- Another skill has a tighter, more specific trigger for the same request.

## Example user requests
- "Apply visual regression guardian to improve this feature."
- "Use visual regression guardian and give me the concrete deliverables."
- "Can you run a full visual regression guardian pass on this repo?"
- "I need step-by-step execution using visual regression guardian."
## Goal
Prevent visual drift and catch subtle regressions in high-impact UIs.

## When to use
- PRODUCTION+.
- After tokens and signature patterns exist.
- Before client handoff.

## Minimal inputs (ask only if missing)
- Routes to baseline.
- Viewports.
- Light/dark variants.

## Procedure (MUST)
1) Add and configure Playwright.
2) Create `docs/visual_regression.md` from template.
3) Implement visual tests for key routes and critical states.
4) Define snapshot update policy.
5) Integrate into CI.

## Outputs (MUST produce)
- `docs/visual_regression.md`.
- `tests/visual/*.spec.*`.
- Playwright config + report artifacts.

## Notes
Snapshots are reviewed artifacts, not auto-updated noise.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardohg1998-create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
