---
name: frontend-vue3
description: Build, maintain, and debug Vue 3 frontends (Vite + TypeScript). Use when this capability is needed.
metadata:
  author: kieutrongthien
---

# Frontend - Vue 3

## When to use this skill
- Feature work or fixes in Vue 3 projects scaffolded with Vite.
- Creating pages/components, routing, forms, state (Pinia), or API integration.
- Testing, linting, performance, or accessibility improvements in the Vue app.

## Quick start
1. Install: `npm ci` (or `pnpm i`, `yarn` as project standard).
2. Env: copy `.env.example` -> `.env`; set `VITE_API_URL` and other `VITE_` vars.
3. Dev server: `npm run dev`; build: `npm run build`; preview: `npm run preview`.
4. Quality: `npm run lint` for ESLint; `npm run test` (Vitest) if present.

## Project structure basics
- `src/main.ts`: app bootstrap; registers router, Pinia, global styles.
- `src/router/*`: route definitions; lazy-load views to shrink bundles.
- `src/stores/*`: Pinia stores for shared state.
- `src/components/*`: reusable UI; keep props typed and emit events explicitly.
- `src/composables/*`: extract logic into composables for reuse/testability.

## Coding principles
- Keep components small; move logic to composables; keep stores focused and typed.
- Validate inputs, handle loading/error states explicitly, and surface user-friendly messages.
- Accessibility first: semantic elements, focus management, keyboard support.
- Enforce lint/type/test/build before merge (see `scripts/dev-check.sh`).

## Patterns and snippets
- Composition, router, Pinia, form, and HTTP client templates live in references/snippets.md.
- Prefer route-level code splitting; centralize HTTP client (base URL, auth, error normalization).
- Use `v-model` + client-side validation; show per-field API errors.

## Testing
- Component tests with Vitest + Vue Test Utils; mock stores and router as needed.
- Prefer testing behavior (rendered text, emitted events) over implementation details.

## Performance and DX
- Use `<script setup>` and `<Suspense>` for async components when helpful.
- Split vendor chunks via Vite config if bundles grow large.
- Keep components small; extract logic to composables; avoid unnecessary reactive nesting.

## Accessibility and UI
- Use semantic elements first; bind `aria-*` labels for interactive controls.
- Manage focus on navigation or modal open/close; ensure keyboard access.

## Bundled resources
- scripts/dev-check.sh: run before committing/raising a PR to ensure lint, type-check, tests, and build all pass with the detected package manager.
- references/coding-standards.md: quick guardrails on component sizing, typing, accessibility, and pre-merge checks.
- references/best-practices.md: deeper guidance for routing, state, styling, API, testing, performance, and a11y decisions.
- references/snippets.md: ready-to-use templates for composition API, routing, Pinia, forms, and HTTP client setup.
- assets/pr-template.md: use when opening PRs so reviewers see scope, testing proof, and UI evidence (desktop/mobile, a11y/perf notes).

## Delivery checklist
- Lint and tests clean; types pass.
- No console logs left; env vars documented; API errors surfaced to users.
- Routes lazy-loaded; bundle size reasonable; loading states present for async flows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kieutrongthien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
