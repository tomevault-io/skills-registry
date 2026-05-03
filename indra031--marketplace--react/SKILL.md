---
name: react
description: > Use when this capability is needed.
metadata:
  author: indra031
---

# React Best Practices

Standards for React web and React Native applications. These conventions are grounded in
patterns from existing MindCTI projects (Authorization, Checkout, Selfcare, MobileSelfcare)
and general React ecosystem best practices.

## When This Applies

- Creating a new React component, page, or feature
- Reviewing or refactoring existing React code
- Setting up state management, routing, or forms
- Writing tests for React components or services
- Working on React Native mobile screens

## Core Principles

1. **Function components + hooks** — never write class components in new code
2. **Redux Toolkit** for shared app state; local `useState`/`useContext` for component-scoped state
3. **@emotion + @theme-ui** for styling — use the `sx` prop and theme tokens
4. **react-hook-form** for all form handling — never manage form state manually
5. **JavaScript** — match existing codebases; use JSDoc for documentation
6. **Custom `RestClient`** with `fetch` for API calls — no axios
7. **React Testing Library** for new tests — test user behavior, not implementation

## Quick Reference — Top 15 Rules

1. Always use function components with hooks
2. Use `useState` for local UI state; Redux Toolkit for shared/server state
3. Use `createSlice` + `createAsyncThunk` with `builder.addCase()` for Redux
4. Style with Theme-UI `sx` prop — use theme tokens, never hardcode colors/spacing
5. Use `react-hook-form` with `register()` for simple inputs, `Controller` for complex ones
6. Use React Router v6 (`Routes`, `useNavigate`) for new web apps
7. API calls go through a `RestClient` class using `fetch` — never call `fetch` directly in components
8. Name files PascalCase for components, use `index.js` barrel exports
9. Use `useIntl()` hook or `<FormattedMessage>` for all user-facing strings
10. Wrap apps with providers: IntlProvider → ThemeProvider → CacheProvider → Redux Provider
11. Create custom hooks for reusable stateful logic — prefix with `use`
12. Use `React.memo()` only when profiling shows unnecessary re-renders
13. Prefer named exports from barrel files; default export from single-component files
14. Co-locate tests in `__tests__/` directories next to the code they test
15. Use `redux-mock-store` and `@testing-library/react` for component tests

## Detailed Reference

For deeper guidance, read the relevant doc:

| Topic | Doc | When to read |
|---|---|---|
| Components & hooks | [docs/component-architecture.md](docs/component-architecture.md) | Creating/reviewing components |
| State management | [docs/state-management.md](docs/state-management.md) | Redux slices, Context, async logic |
| Styling | [docs/styling.md](docs/styling.md) | Theme-UI, emotion, responsive design |
| Forms | [docs/forms.md](docs/forms.md) | react-hook-form, validation |
| Routing | [docs/routing.md](docs/routing.md) | React Router v5/v6, protected routes |
| API patterns | [docs/api-patterns.md](docs/api-patterns.md) | RestClient, fetch, error handling |
| Testing | [docs/testing.md](docs/testing.md) | RTL, Jest, snapshot vs interaction |
| Naming conventions | [docs/naming-conventions.md](docs/naming-conventions.md) | Files, components, Redux, hooks |
| i18n | [docs/i18n.md](docs/i18n.md) | react-intl, translations |

## General React Notes

These apply to any React app, not just MindCTI projects:

- **Hooks rules**: never call hooks conditionally or inside loops; always at the top level of the component
- **Key prop**: always provide a stable, unique `key` when rendering lists — never use array index as key for dynamic lists
- **Effect cleanup**: always return a cleanup function from `useEffect` when adding listeners or timers
- **Dependency arrays**: include every variable referenced inside `useEffect`/`useCallback`/`useMemo` — the linter is right
- **Avoid prop drilling**: when passing props through 3+ levels, consider Context or Redux
- **Error boundaries**: wrap major UI sections in error boundaries so a crash in one panel doesn't break the whole page

## MIND CTI Specific Notes

- All existing web apps use **JavaScript** (not TypeScript) — new code in existing apps should match
- All web apps share the **@emotion + @theme-ui** stack with multi-tenant branding support
- All web apps use a custom **RestClient** singleton (`FacadeAPI`, `AuthAPI`) — not axios
- The mobile app (MobileSelfcare) is **React Native 0.68** with older patterns (class components, Redux thunks) — new screens should use function components and hooks
- i18n: web apps use **react-intl**; mobile uses **react-native-globalize**

---
> Source: [indra031/marketplace](https://github.com/indra031/marketplace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
