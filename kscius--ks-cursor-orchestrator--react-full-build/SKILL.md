---
name: react-full-build
description: React/Next.js end-to-end implementation with UI verification. Use for React or Next.js tasks that should be built, validated, and shipped completely. Use when this capability is needed.
metadata:
  author: kscius
---

## When to use

- React/Next.js end-to-end implementation with UI verification.

> **On-demand loading:** Read this skill only when the task clearly matches the description above or trigger phrases below. Do not load for unrelated work.

# react-full-build

Use this skill for React / Next.js tasks that should be implemented and validated end-to-end.

## Objective
Ship a frontend or fullstack change with strong implementation and UI verification.

## Workflow
1. Inspect:
   - app/router structure
   - relevant components
   - state management
   - API/data fetching
   - tests
   - design system / shared UI
2. Prefer existing patterns over introducing new abstractions.
3. Apply these skills when relevant:
   - `react-dev`
   - `vercel-react-best-practices`
   - `web-design-guidelines`
   - `webapp-testing`
4. Delegate as needed:
   - `frontend-developer` for UI implementation
   - `fullstack-developer` when frontend + API changes are coupled
   - `debugger` for runtime/render/hydration/test failures
5. Validate with the strongest available subset:
   - lint
   - typecheck
   - unit/integration tests
   - build
   - browser automation / UI checks
   - accessibility review when relevant
6. Fix any failing checks before completion.

## Frontend quality bar
- preserve accessibility
- keep components cohesive
- avoid unnecessary effects
- keep state local when possible
- avoid introducing render-time instability
- preserve loading/error/empty states

## Output format
- Components/pages touched
- Behavior implemented
- Validation performed
- UX/accessibility notes
- Remaining risks

---
> Source: [kscius/KS-Cursor-Orchestrator](https://github.com/kscius/KS-Cursor-Orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
