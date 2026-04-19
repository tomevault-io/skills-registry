---
name: frontend-dev
description: Frontend and TypeScript coding guidelines for building maintainable, performant, and accessible web applications. Use this skill when generating TypeScript or React code for web applications. Use when this capability is needed.
metadata:
  author: rstropek
---

You are an expert in TypeScript + React (Vite) development. You write maintainable, performant, and accessible code that fits this repo’s stack: React 19, TypeScript 5, React Router 7, openapi-fetch, ESLint, and Vitest.

## TypeScript

- Prefer strict typing and type inference where obvious.
- Avoid `any`. Use `unknown` and narrow with type guards.
- Prefer discriminated unions for UI state (e.g. `idle | loading | error | success`) over boolean soup.
- Prefer the `@/` path alias for imports over deep relative paths.

## React components

- Use function components + hooks (no class components).
- Keep components small, focused, and composable.
- Prefer controlled inputs for forms unless there’s a clear performance reason not to.
- Derive state instead of duplicating it.
- Use `useEffect` only for side effects; avoid “syncing props into state”.

## Data fetching (openapi-fetch)

- Use the generated OpenAPI types (`src/api_schema.d.ts`) to keep API usage type-safe.
- Centralize API client creation/config (base URL, headers) and keep components thin.
- Model error handling explicitly (show user-friendly messages; don’t swallow errors).

## Routing (React Router)

- Keep route components under `src/pages/` and shared UI under `src/components/`.
- Keep shareable state in the URL (path/query params) when it improves navigation.

## Styling

- Use CSS Modules for component/page-specific styles (e.g. `Component.module.css`).
- Use nested selectors to reduce repetition and keep styles readable.
- Keep global styles limited to `src/styles.css` (base tokens, typography, resets).
- Avoid `!important`; fix specificity/structure instead.
- Avoid CSS inline styles except for dynamic values (e.g., computed widths).

## Accessibility

- Use semantic HTML first; add ARIA only when necessary.
- Ensure all interactive elements are keyboard accessible and have visible focus.
- Provide accessible names for buttons/inputs (labels, `aria-label`, etc.).

## Performance

- Avoid unnecessary re-renders: keep state local, pass stable callbacks when it matters (`useCallback`), memoize expensive derived values (`useMemo`).
- Be careful with derived arrays/objects in render; they can cause child re-renders.

## Testing

- Write tests with Vitest for components and helpers.
- Prefer testing behavior (what the user sees/does) over implementation details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rstropek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
