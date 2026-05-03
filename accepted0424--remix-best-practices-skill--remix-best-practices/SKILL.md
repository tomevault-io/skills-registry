---
name: remix-best-practices
description: Remix v2 best-practice workflow for feature implementation, refactoring, and code review. Use when working on route modules, loaders/actions, Form vs fetcher decisions, pending/optimistic UI, revalidation, module constraints (.server/.client), and deployment performance patterns in Remix apps. Use when this capability is needed.
metadata:
  author: accepted0424
---

# Remix Best Practices

Execute this workflow for any Remix v2 task so architecture and code choices stay consistent with official guidance.

## Task Intake

Classify the request before coding:

1. `new-feature`: add route, loader/action, forms, and UI states.
2. `mutation-refactor`: convert ad hoc client fetches to action/fetcher patterns.
3. `performance`: improve bundle, cache, prefetch, streaming, or concurrency.
4. `code-review`: find risks and regressions in existing Remix code.

Then load references:

- Always read `references/remix-v2-checklist.md`.
- For feature delivery, read `references/feature-delivery-playbook.md`.
- For mutation and interaction design, read `references/mutation-ui-patterns.md`.
- For review tasks, read `references/review-rubric.md`.

## Core Workflow

1. Identify the route scope and file conventions first.
2. Decide data flow (`loader`/`action`) before writing components.
3. Choose interaction model (`<Form>` or `useFetcher`) intentionally.
4. Choose pending feedback strategy (busy, optimistic, skeleton) based on data reliability.
5. Keep server/client boundaries strict (`.server`/`.client` and side-effect isolation).
6. Confirm revalidation, caching, and prefetch/streaming decisions.
7. Include error handling and accessibility checks before finalizing.
8. Run review gate checks and verify behavior with focused tests.

## Decision Matrix

### `<Form>` vs `useFetcher`

- Choose `<Form>` when submit should navigate or URL should represent the new state.
- Choose `useFetcher` when interaction must stay in place or run concurrently with others.
- Avoid `fetch` in event handlers for route-owned writes unless there is a clear non-Remix integration reason.

### Busy vs Optimistic vs Skeleton

- Choose busy indicators when server result is uncertain or validation failures are common.
- Choose optimistic UI when success rate is high and rollback path is defined.
- Choose skeletons when shape/layout is known and data fetch latency is visible.
- Keep one dominant pending strategy per interaction path.

### Revalidation and `shouldRevalidate`

- Keep default revalidation unless there is measured over-fetch or correctness constraints.
- Add `shouldRevalidate` only with explicit criteria (param changes, mutation targets, or stale windows).
- Document the reason whenever revalidation is suppressed.

### State Placement

- Put shareable/search/sort/filter state in URL search params.
- Put server-owned state in loader/action data.
- Put per-user secrets or durable auth data in cookies/sessions.
- Use local component state only for ephemeral UI behavior.

## Anti-pattern Guardrails

Reject or refactor these patterns:

- Duplicate server data copied into local state without a sync strategy.
- Route writes implemented only via client `fetch` calls with no action contract.
- Pending UX that hides status from keyboard/screen-reader users.
- Server-only imports in client-reachable modules.
- `shouldRevalidate` added to mask incorrect data flow.

## Output Contract

When implementing or reviewing, always provide:

1. Architecture decisions: route/module boundaries, loader/action ownership, interaction model.
2. Concrete code changes: route exports, component integration, and data mutation paths.
3. Pending and error behavior: what users see during request, success, and failure.
4. Verification: minimal tests or manual checks for navigation, mutation, revalidation, and boundaries.
5. Risk notes: tradeoffs and follow-up work when deviating from defaults.

## Review Priority Order

When request type is `code-review`, prioritize checks in this order:

1. Correctness and data integrity.
2. Security and server/client boundary leaks.
3. UX regressions in pending/error states.
4. Performance and caching behavior.
5. Code maintainability and consistency.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/accepted0424) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
