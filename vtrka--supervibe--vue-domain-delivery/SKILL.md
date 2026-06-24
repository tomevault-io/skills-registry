---
name: vue-domain-delivery
description: >- Use when this capability is needed.
metadata:
  author: vTRKA
---

# Vue Domain Delivery

## Overview

Vue Domain Delivery keeps Vue 3 work inside the Composition API and reactivity
contract. It separates Single File Components, composables, Pinia stores,
router guards, slots, forms, async state, and tests so reactive updates remain
predictable and accessible.

## When to Use

Use for `.vue` components, `<script setup>`, typed props/emits, composables,
Pinia stores, Vue Router routes/guards, forms, slots, provide/inject,
Suspense/async components, reactivity pitfalls, accessibility, and Vitest/Vue
Test Utils coverage.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source
first, preserve evidence, follow existing Vue conventions, verify before
completion claims, and cap confidence when reactivity, a11y, async, or tests
are not proven.

## Step 0 - Read source of truth

1. Read the user request, `AGENTS.md`, task graph item, and owned write set.
2. Inspect package manifests, Vue version, Vite config, router, Pinia setup,
   component library, lint rules, and nearest tests.
3. Search for adjacent SFCs, composables, stores, route guards, typed
   InjectionKeys, form helpers, async components, and test utilities.
4. Use CodeGraph for unfamiliar UI areas and CodeGraph or symbol search before
   renaming exported components, composables, store actions, or route records.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no Vue implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the Vue part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
Template + UI state? -> .vue SFC with <script setup lang="ts">.
Reusable reactive logic? -> composable useX(inputs) returning refs/computed/functions.
Cross-route or devtools-visible state? -> namespaced Pinia setup store.
Derived from reactive inputs? -> computed, never watch writing a derived ref.
External side effect? -> watch/watchEffect with cleanup or lifecycle hook.
Parent-child contract? -> typed props/emits; v-model emits update:modelValue.
Flexible rendering? -> typed slots with explicit fallback and accessibility semantics.
Route concern? -> Vue Router route record/guard, not ad hoc component redirect logic.
Async display? -> explicit loading/error/empty/success or Suspense with error handling.
```

## Procedure

1. Define the slice: component/composable/store/route, inputs, outputs, visible
   states, reactive owner, verification command, and rollback path.
2. Map boundaries. SFCs own template and local UI state; composables own
   reusable reactive behavior; Pinia owns shared domain state; router guards own
   navigation decisions; provide/inject owns typed cross-cutting context.
3. Model props/emits. Props are readonly inputs, emits are the only upward
   mutation path, `v-model` uses `modelValue`/`update:modelValue` or typed
   named models, and default values are explicit.
4. Preserve reactivity. Keep `reactive()` objects intact or use `toRefs`; use
   `ref` for primitives; use `computed` for derived values; avoid destructuring
   props without `toRefs`/compiler-safe patterns.
5. Use watchers only for effects. Watch network calls, storage, DOM, timers, or
   subscriptions with cleanup via `onCleanup`, `onUnmounted`, or
   `onScopeDispose`. Never use a watcher to maintain a value a computed can
   derive.
6. Design stores deliberately. Introduce Pinia only for shared state crossing
   route/component boundaries, namespace ids by domain, keep actions typed, and
   avoid persisting tokens or sensitive state.
7. Handle forms as contracts. Bind labels and errors, validate at field and
   submit boundaries, preserve user input on failure, prevent duplicate submit,
   and emit typed submit/update events.
8. Treat slots as public APIs. Name slots for intent, define slot props, provide
   fallback content, preserve semantic structure, and avoid requiring consumers
   to know internal DOM.
9. Handle async state explicitly. For `useFetch`-style composables or async
   components, expose pending/error/data state, support retry, and pair
   Suspense with error capture when used.
10. Write focused tests first when behavior changes. Cover render states,
    emitted events, router navigation, Pinia actions, composable cleanup,
    keyboard behavior, slot fallback/override, and reactivity updates.
11. Repair loop: on failure, localize to prop/emit contract, reactive primitive,
    watcher cleanup, store boundary, router guard, or test fixture; fix narrowly
    and rerun the same scoped command.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Create a searchable user picker:

1. `<UserPicker>` receives `modelValue` and emits `update:modelValue`; it does
   not mutate the selected user prop.
2. `useUserSearch(queryRef)` owns debounced async search, exposes
   `{ status, results, error, retry }`, and cancels stale requests on cleanup.
3. Pinia is not introduced because the state belongs to one component tree.
4. Tests cover label/combobox behavior, empty query, loading, result selection,
   keyboard navigation, network error retry, and cleanup of debounced searches.

## Common rationalizations

- "A watcher is easier than computed" fails when it stores derived state and
  creates timing bugs.
- "This store may be useful later" fails when local state is enough and global
  state makes tests and SSR harder.
- "Slots are just markup" fails once consumers depend on slot props and DOM
  semantics as a public component API.

## Red flags

- Props are mutated or copied into local state without a sync strategy.
- `watch` updates a value derived only from reactive inputs.
- `reactive()` is destructured and loses reactivity.
- Pinia store id is `main`, `app`, or `global`, or persists sensitive data.
- `v-html` renders user-controlled content without sanitization.
- Router redirects live inside render effects instead of guards or explicit
  navigation handlers.
- Tests assert CSS selectors while role/text/emit assertions are available.

## Checklist

- Composition API and `<script setup lang="ts">` are used for new code.
- Props, emits, slots, provide/inject, and stores are typed.
- Reactivity is preserved; derived values are computed.
- Forms expose validation, pending, success, and error states.
- Async state has loading, empty, error, success, retry, and cleanup behavior.
- Accessibility and keyboard behavior are covered.
- Rollback path is named: revert SFC, disable route, remove store usage, or
  restore previous composable.

## Failure modes

- The Vue specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `status`: PASS, BLOCKED, PARTIAL, or DEFERRED.
- `slice`: SFCs, composables, stores, routes, and forms changed.
- `reactivityModel`: refs/reactive/computed/watch choices and cleanup proof.
- `contracts`: props, emits, slots, store actions, route guards.
- `a11yAndForms`: labels, keyboard, validation, pending/error states.
- `asyncState`: loading, empty, error, success, retry, and cancellation.
- `tests`: scoped commands and cases, or final-gate deferral reason.
- `rollback`: revert, flag, route disable, or state compatibility path.
- `confidence`: score with caps from missing reactivity/a11y/test proof.

## Guard rails

- Do not implement Vue changes from a generic skill when a stack-specific owner, migration path, or runtime boundary is missing.
- Avoid broad rewrites, dependency swaps, large test suites, or speculative architecture changes during fast MVP execution.
- Reject work that skips source search, rollback naming, or scoped verification for the changed slice.
- Stop when local project conventions, version constraints, permissions, data ownership, or deployment target are unknown.

## Verification

- Run the scoped command required by the active graph when policy allows, such
  as `vitest run <path>`, `vue-tsc --noEmit`, lint, or router/store tests.
- Verify no Vue warnings, prop mutation, untyped emits, or lost reactivity.
- Verify forms and interactive components with keyboard and role/name checks.
- Verify async composables cancel stale work and expose error/retry paths.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

- examples/delivery.md - worked Vue implementation example, anti-example, and verification fixture.
- domain-packs/vue.md - Vue practice pack with review matrix, rollback prompts, and MVP guard rails.
- evals/regression.json - regression eval pack for happy path, failure path, boundary/null, and rollback coverage.

## Related

- `supervibe:nuxt-domain-delivery`
- `supervibe:frontend-ui-engineering`
- `supervibe:tdd`
- `supervibe:test-strategy`
- `supervibe:code-search`
- `supervibe:verification`

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-02 -->
