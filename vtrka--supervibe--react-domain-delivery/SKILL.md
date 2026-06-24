---
name: react-domain-delivery
description: Use WHEN implementing or reviewing React component, hook, form, state, accessibility, data-fetching, error/loading, performance, or test work that needs React-specific delivery discipline beyond generic frontend practice. TO deliver stack-specific production changes with framework-native practices, verification evidence, and rollback discipline.
metadata:
  author: vTRKA
---

# React Domain Delivery

## Overview

React Domain Delivery turns a React task into a bounded UI state-machine
change. It keeps component boundaries small, state colocated, hooks honest,
async states explicit, accessibility native-first, forms typed, performance
measured, and tests focused on user-observable behavior.

## When to Use

Use for React components, custom hooks, context, reducers, forms, Suspense,
Error Boundaries, data-fetching integration, client islands inside SSR
frameworks, accessibility behavior, bundle or render performance, and React
Testing Library coverage.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source
first, prefer established project primitives, preserve evidence, verify before
completion claims, and cap confidence when state, a11y, async, or tests are not
proven.

## Step 0 - Read source of truth

1. Read the user request, `AGENTS.md`, task graph item, and owned write set.
2. Inspect package manifests, React version, router/framework, test runner,
   component library, data layer, form library, lint rules, and nearest tests.
3. Search for adjacent components, hooks, context providers, error boundaries,
   Suspense usage, form patterns, and accessibility helpers before adding new
   abstractions.
4. Use CodeGraph for unfamiliar UI areas and CodeGraph or symbol search before
   renaming, moving, extracting, or changing exported components/hooks.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no React implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the React part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
Is the component pure markup from props? -> presentational component, no local state/effects.
Does it own one concern of UI state? -> colocate useState/useReducer at lowest consumer.
Are there >3 related state/effect pieces? -> extract a custom hook returning a tagged state.
Is state shared by siblings or routes? -> lift one level, context for stable values, store for frequent updates.
Is data needed for first paint? -> framework/server fetch where available, Suspense/query boundary otherwise.
Is this a mutation or form? -> schema/field validation, pending/error/success states, double-submit guard.
Does it use browser APIs/subscriptions? -> effect with cleanup or useSyncExternalStore; no derived-state effects.
Does it cross a server/client framework boundary? -> keep server markup above, client island as deep as possible.
Is render performance suspected? -> profile first, then memoize/split/lazy-load with evidence.
```

## Procedure

1. Define the slice: component/hook/form, inputs, outputs, visible states, data
   owner, verification command, and rollback path.
2. Draw component boundaries. Keep data orchestration in containers/server
   components, rendering in presentational components, side effects in hooks,
   and reusable field behavior in form primitives.
3. Enumerate UI states before code: idle, loading, empty, partial, success,
   validation error, network error, optimistic, stale, disabled, and forbidden
   where relevant.
4. Choose the state model. Prefer local state for isolated UI, `useReducer` for
   transition-heavy flows, context for stable cross-cutting values, and a store
   only for frequent shared updates or cross-route state.
5. Design hooks as contracts. A custom hook owns one concern, accepts explicit
   inputs, cleans subscriptions, returns named values or a discriminated union,
   and never hides render-only derived state in `useEffect`.
6. Respect server/client splits. In Next.js or similar frameworks, keep
   `"use client"` leaves small, pass server-rendered data as props, never import
   server-only modules into client files, and keep secrets off the client.
7. Implement data fetching deliberately. Prefer existing query/cache tools,
   model loading/error/empty states explicitly, avoid waterfalls, and pair
   Suspense with an Error Boundary at a meaningful UX boundary.
8. Build forms as state machines. Validate fields and submit payloads, preserve
   user input on failure, show field-level errors, disable duplicate submits,
   provide optimistic rollback when used, and keep submit effects observable.
9. Run an accessibility pass while coding. Use semantic HTML first, labels for
   controls, keyboard-reachable interactions, visible focus, live regions for
   async feedback, correct modal focus management, and WCAG-conscious disabled
   and error states.
10. Measure performance only where risk exists. Use React DevTools profiler,
    route bundle output, and runtime evidence before adding `memo`,
    `useMemo`, `useCallback`, virtualization, or lazy loading.
11. Test behavior first. Use React Testing Library queries by role, label, and
    text; cover every enumerated state, keyboard interaction, validation path,
    and network boundary with MSW or the project-standard mock.
12. Repair loop: on failure, localize to boundary, state model, effect cleanup,
    async contract, or test fixture; fix the smallest cause and rerun the same
    scoped command. Stop if the fix requires product, data, or architecture
    approval.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Build an editable invoice line-item table:

1. Parent fetches invoice data; table receives rows and mutation callbacks as
   props.
2. Row edit state is local to each row; totals are `useMemo` only after a
   profiler shows expensive recalculation, otherwise plain derived render data.
3. Validation errors are field-specific, announced with `aria-describedby`, and
   submit uses a pending state plus duplicate-submit guard.
4. Tests cover empty invoice, row edit, invalid quantity, keyboard save/cancel,
   server conflict, optimistic rollback, and focus returning to the edited row.

## Good and bad delivery paths

Good delivery path: deliver as a bounded React state machine with
server/container data ownership, local row edit state,
pending/error/empty/conflict states, semantic form controls, and optimistic
rollback only where supported. Runtime-specific tests include React Testing
Library role/name flows for edit, validation, keyboard save/cancel, focus
return, loading/empty/error states, MSW or project network fixtures for
conflict/retry, and browser/a11y evidence when interaction risk is high.
Rollback reverts the component or flag and restores previous fetch/form
behavior without changing the API contract. Failure boundaries are validation,
duplicate submit, stale data conflict, network error, effect cleanup, focus
loss, accessibility announcement failure, and optimistic rollback drift.

Bad unsafe path: synchronize derived totals through useEffect, keep all rows
in a global store, submit without pending or conflict handling, and verify
only with snapshots or test ids. That path has no runtime-specific tests for
the changed stack surface, no concrete rollback beyond hope or manual cleanup,
and weak failure boundaries for validation, duplicate submit, stale data
conflict, network error, effect cleanup, focus loss, accessibility
announcement failure, and optimistic rollback drift.

## Common rationalizations

- "A quick `useEffect` can sync this derived value" fails because render can
  compute derived data without introducing stale intermediate state.
- "Context is simpler than prop passing" fails when the value changes on every
  keystroke and rerenders an entire subtree.
- "This is just visual" fails when keyboard, screen reader, loading, error, and
  empty states are user-visible behavior.

## Red flags

- Top-level client boundary wraps mostly static/server-renderable markup.
- `useEffect` writes state that could be computed during render.
- Component has loading/error branches but no empty or partial branch.
- Form submit lacks pending, validation failure, and duplicate-submit behavior.
- `memo` or callback stabilization appears without profiler or bundle evidence.
- Tests query CSS classes or test ids while roles/labels are available.

## Checklist

- Component, hook, form, and data boundaries are explicit.
- State is colocated or deliberately shared with a named owner.
- All visible states and transitions are represented in UI and tests.
- Effects synchronize with external systems and have cleanup.
- Accessibility behavior is semantic, keyboard-tested, and announced where needed.
- Performance optimizations are evidence-backed.
- Rollback path is named: revert component, flag route, disable feature, or
  restore previous data-fetch/form behavior.

## Failure modes

- The React specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `status`: PASS, BLOCKED, PARTIAL, or DEFERRED.
- `slice`: components, hooks, forms, routes, and state owners changed.
- `stateModel`: visible states, owner, transitions, and illegal transitions.
- `serverClientBoundary`: server-rendered areas, client islands, and secret/data
  safety notes when applicable.
- `a11yAndForms`: keyboard, labeling, live-region, validation, and submit proof
  or gaps.
- `asyncAndErrors`: loading, empty, error, stale, Suspense, and Error Boundary
  handling.
- `performance`: profiler, bundle, render-risk evidence or reason not needed.
- `tests`: scoped commands and cases, or final-gate deferral reason.
- `rollback`: revert, flag, or compatibility path.
- `confidence`: score with caps from missing state/a11y/performance/test proof.

## Guard rails

- Do not implement React changes from a generic skill when a stack-specific owner, migration path, or runtime boundary is missing.
- Avoid broad rewrites, dependency swaps, large test suites, or speculative architecture changes during fast MVP execution.
- Reject work that skips source search, rollback naming, or scoped verification for the changed slice.
- Stop when local project conventions, version constraints, permissions, data ownership, or deployment target are unknown.

## Verification

- Run the scoped command required by the active graph when policy allows, such
  as `npm test -- <component>`, `vitest run <path>`, `tsc --noEmit`, lint, and
  build.
- For accessibility-sensitive work, include keyboard walkthrough and role/name
  assertions or browser/a11y evidence.
- For async UI, exercise loading, empty, error, success, and retry paths.
- For performance work, include profiler or bundle evidence before and after.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

Use local support files through progressive disclosure; keep the main SKILL.md as the operating contract and load deeper resources only when the active task needs them:
- Read `references/practice-pack.md` when react-domain-delivery needs deeper practice guidance, source evidence anchors, risk checks, or a final checklist beyond the core procedure.
- Run `scripts/self-check.mjs --check --json` after editing this react-domain-delivery resource tree or before claiming deterministic support-file readiness; use `--help` for options and `--dry-run` for read-only preview semantics.
- Use `evals/regression.json` when calibrating react-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.
- Open `examples/workflow.md` when a concrete react-domain-delivery workflow example is needed for sequencing, evidence selection, or anti-example comparison.
- Use `templates/output-contract.md` when emitting react-domain-delivery-report so status, evidence, confidence, blockers, risks, rollback, and nextAction stay consistent.

## Related

- `supervibe:frontend-ui-engineering`
- `supervibe:browser-runtime-verification`
- `supervibe:tdd`
- `supervibe:test-strategy`
- `supervibe:code-search`
- `supervibe:verification`

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
