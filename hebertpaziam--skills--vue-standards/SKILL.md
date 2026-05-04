---
name: vue-standards
description: Apply Vue 3 standards to new features and refactors, with focus on SFCs, Composition API, `<script setup lang="ts">`, predictable reactivity, and internal references for state, router, testing, styling, and boundaries. Do NOT trigger for routine TypeScript edits already covered by typescript-standards. Use when this capability is needed.
metadata:
  author: hebertpaziam
---

# Vue 3 Standards

## When to Use

- Creating or refactoring Vue 3 features.
- Standardizing components, composables, stores, routes, and forms in Vue projects.
- Orchestrating Vue tasks involving `*.vue`, `*.ts`, stores, router, component styles, or test files.

## Goal

- Standardize Vue 3 with SFCs, `<script setup lang="ts">`, Composition API, Pinia, Vue Router, testability, and accessibility.
- Concentrate detailed rules in local references to avoid duplication and maintain clear precedence.
- Preserve adherence to the test, build, lint, and styling tools already configured in the project.

## LIFT Principle

Before creating any new component, composable, store, route module, helper, or test, apply the LIFT principle:

- **Locate**: Find existing SFCs, composables, stores, helpers, contracts, and references in the project and domain.
- **Identify**: Determine what already solves part of the problem and where there is potential duplication.
- **Find**: Find the smallest coherent point of reuse, extension, or composition before introducing new code.
- **Try to be DRY**: Eliminate real duplication without creating premature abstractions, opaque APIs, or unnecessary shared state.

## Integration with Other Skills

- When modifying general typing, shared contracts, or TypeScript modeling decisions, follow `typescript-standards`.
- When suggesting or running project scripts, respect the stack and scripts already configured in the project.
- When preparing commits, follow `git-commit`.
- When modifying component styles or template classes, follow [references/component-styles.md](references/component-styles.md).
- When creating or modifying test files, follow [references/testing-fundamentals.md](references/testing-fundamentals.md).
- When working with routing, query params, guards, or lazy loading, follow the `router-*` references.
- In case of conflict, this order prevails: topic-specific local references, `typescript-standards` for general TypeScript rules, `git-commit` for commits, and `vue-standards` for Vue architecture, reactivity, composition, and accessibility.

## Main Rules

- Vue 3 only.
- Apply LIFT before introducing new components, composables, stores, route modules, helpers, or tests.
- For new code, use Single-File Components with `<script setup lang="ts">` by default.
- For new code, use Composition API by default.
- Do not rewrite stable Options API components without real need.
- Use `computed` for derived state; do not use `watch` or `watchEffect` for pure derivation.
- Use `watch` and `watchEffect` only for side effects, external synchronization, or controlled async reactions.
- Reusable logic with state should become a composable.
- Shared state across application areas should use Pinia, not ad hoc reactive singletons.
- State that needs to survive navigation, be shareable by URL, or be deep-linkable should use Vue Router.
- Do not use mixins as a pattern in Vue 3.
- Keep templates simple, semantically correct, and without excessive logic.
- Maintain WCAG AA baseline and do not expose accessibility as configurable API without concrete need.
- Never use untrusted templates or render arbitrary content as a Vue template.
- When creating or updating tests, reuse only the test stack and utilities already configured in the project.

## Procedure

1. Apply LIFT to locate, identify, and reuse what already exists before creating new structures.
2. Model the feature with SFC, `<script setup lang="ts">`, and Composition API for new code.
3. Keep local state in the component, extract reusable logic to composables, and move shared state to Pinia when necessary.
4. Use `computed` for derivations and limit `watch` to real side effects.
5. If there is navigable or URL-shareable state, model it in the router.
6. If there are component styles, follow [references/component-styles.md](references/component-styles.md).
7. If there are tests, follow [references/testing-fundamentals.md](references/testing-fundamentals.md).
8. If there is shared typing or contracts, also align with `typescript-standards`.
9. Review semantics, accessibility, reactive predictability, and adherence to the project stack.

## Quality Checklist

- New code in SFC with `<script setup lang="ts">`, unless there is real technical justification.
- LIFT applied before introducing new structures or duplicating implementations.
- Composition API used by default in new code.
- `computed` used for derived state.
- `watch` and `watchEffect` used only when there is a real side effect.
- Composables extracted when there is reusable logic with state.
- Pinia used for shared state when necessary.
- Router used for URL-driven state when necessary.
- If styles were changed, [references/component-styles.md](references/component-styles.md) was followed.
- If tests were changed, [references/testing-fundamentals.md](references/testing-fundamentals.md) was followed.
- If general typing was changed, `typescript-standards` was followed.
- Template simple, semantic, and accessible.
- No parallel build, test, or styling stack was introduced.

## Notes

- This skill does not standardize a single test, build, or SSR tool; always use the project's effective configuration.
- Nuxt, SSR, and hydration details live in the boundary references, not in the main skill body.
- `component-styles.md` is neutral regarding the project's styling stack; follow the existing tokens, conventions, and utilities.
- For project scripts, respect the stack and scripts already configured in the project.
- For commits, use `git-commit`.

## Core

Consult when working with SFC structure, components, props, events, slots, or lifecycle.

- SFC structure: [references/core-sfc-structure.md](references/core-sfc-structure.md)
- `<script setup>`: [references/core-script-setup.md](references/core-script-setup.md)
- Components: [references/core-components.md](references/core-components.md)
- Props and emits: [references/core-props-and-emits.md](references/core-props-and-emits.md)
- `defineModel`: [references/core-define-model.md](references/core-define-model.md)
- Slots: [references/core-slots.md](references/core-slots.md)
- Template refs: [references/core-template-refs.md](references/core-template-refs.md)
- Provide / inject: [references/core-provide-inject.md](references/core-provide-inject.md)
- Lifecycle hooks: [references/core-lifecycle-hooks.md](references/core-lifecycle-hooks.md)
- Custom directives: [references/core-custom-directives.md](references/core-custom-directives.md)

## Reactivity

Consult when choosing between reactivity primitives or implementing watchers and effects.

- `ref` vs `reactive`: [references/reactivity-refs-vs-reactive.md](references/reactivity-refs-vs-reactive.md)
- `computed` vs `watch`: [references/reactivity-computed-vs-watch.md](references/reactivity-computed-vs-watch.md)
- `watch` vs `watchEffect`: [references/reactivity-watch-vs-watch-effect.md](references/reactivity-watch-vs-watch-effect.md)
- Shallow APIs: [references/reactivity-shallow-apis.md](references/reactivity-shallow-apis.md)

## Composables

Consult when extracting reusable logic, handling async composables, or sharing state.

- Composable design: [references/composables-design.md](references/composables-design.md)
- Async composables: [references/composables-async.md](references/composables-async.md)
- Sharing state in composables: [references/composables-sharing-state.md](references/composables-sharing-state.md)

## Templates

Consult when working with conditional rendering, lists, or class/style bindings.

- Conditional rendering: [references/template-conditional-rendering.md](references/template-conditional-rendering.md)
- List rendering and keys: [references/template-list-rendering-and-keys.md](references/template-list-rendering-and-keys.md)
- Class and style bindings: [references/template-class-and-style-bindings.md](references/template-class-and-style-bindings.md)

## Rendering

Consult when dealing with performance, async components, Suspense, or transition effects.

- Performance basics: [references/rendering-performance-basics.md](references/rendering-performance-basics.md)
- Async components: [references/rendering-async-components.md](references/rendering-async-components.md)
- Suspense: [references/rendering-suspense.md](references/rendering-suspense.md)
- Teleport, KeepAlive, and Transition: [references/rendering-teleport-keepalive-transition.md](references/rendering-teleport-keepalive-transition.md)

## State Management

Consult when designing local state, Pinia stores, or URL-driven state.

- Local component state: [references/state-local-component-state.md](references/state-local-component-state.md)
- Pinia stores: [references/state-pinia-stores.md](references/state-pinia-stores.md)
- Pinia store design: [references/state-pinia-store-design.md](references/state-pinia-store-design.md)
- URL as state: [references/state-url-as-state.md](references/state-url-as-state.md)

## Router

Consult when defining routes, navigation, guards, lazy loading, or data fetching.

- Define routes: [references/router-define-routes.md](references/router-define-routes.md)
- Navigation: [references/router-navigation.md](references/router-navigation.md)
- Route params and query: [references/router-route-params-and-query.md](references/router-route-params-and-query.md)
- Guards: [references/router-guards.md](references/router-guards.md)
- Lazy loading: [references/router-lazy-loading.md](references/router-lazy-loading.md)
- Data fetching with router: [references/router-data-fetching.md](references/router-data-fetching.md)
- Router testing: [references/router-testing.md](references/router-testing.md)

## Forms

Consult when implementing form bindings or validation.

- `v-model`: [references/forms-v-model.md](references/forms-v-model.md)
- Form validation: [references/forms-validation.md](references/forms-validation.md)

## TypeScript in Vue

Consult when typing SFC macros, template refs, or injection.

- TypeScript in SFC macros: [references/typescript-sfc-macros.md](references/typescript-sfc-macros.md)
- TypeScript in template refs and injection: [references/typescript-template-refs-and-injection.md](references/typescript-template-refs-and-injection.md)

## Styling and Accessibility

Consult when working with component styles, accessibility, or security.

- Component styles: [references/component-styles.md](references/component-styles.md)
- Accessibility: [references/accessibility.md](references/accessibility.md)
- Security: [references/security.md](references/security.md)

## Testing

Consult when writing or updating tests for components, composables, stores, or routes.

- Testing fundamentals: [references/testing-fundamentals.md](references/testing-fundamentals.md)
- Testing components: [references/testing-components.md](references/testing-components.md)
- Testing composables: [references/testing-composables.md](references/testing-composables.md)
- Testing Pinia and Router: [references/testing-pinia-and-router.md](references/testing-pinia-and-router.md)
- E2E testing: [references/testing-e2e.md](references/testing-e2e.md)

## Boundaries

Consult when dealing with Vite, SSR, hydration, or Nuxt integration.

- Vite: [references/boundaries-vite.md](references/boundaries-vite.md)
- SSR and hydration: [references/boundaries-ssr-and-hydration.md](references/boundaries-ssr-and-hydration.md)
- Nuxt: [references/boundaries-nuxt.md](references/boundaries-nuxt.md)

---
> Source: [hebertpaziam/skills](https://github.com/hebertpaziam/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
