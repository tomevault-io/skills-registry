---
name: vue
description: Vue 3 projects should follow these guardrails. Use when this capability is needed.
metadata:
  author: hivellm
---
<!-- VUE:START -->
# Vue Framework Rules

**CRITICAL**: Vue 3 projects should follow these guardrails.

## Quality Commands
- Lint: `npm run lint`
- Unit tests: `npm run test:unit`
- e2e tests (Cypress/Playwright): `npm run test:e2e`
- Build: `npm run build`

## Project Structure
- Place feature modules in `src/modules/<feature>/`
- Keep reusable components in `src/components`
- Shared composables go in `src/composables`
- Centralize Pinia/Vuex stores under `src/stores`

## Implementation Guidelines
- Adopt `<script setup>` with TypeScript and define props via `defineProps`
- Leverage `defineEmits` for typed event emission
- Lazy-load routes with dynamic imports in `router/index.ts`
- Ensure global styles scoped via `:global` or dedicated CSS files

## Pre-Commit Commands
```bash
npm run lint
npm run test:unit
npm run build
```

## Documentation
- Update `/docs/vue-architecture.md` with module tree changes
- Document composables in `/docs/composables.md`
- Add component usage examples to `/docs/ui-components.md`

<!-- VUE:END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hivellm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
