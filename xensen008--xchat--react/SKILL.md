---
name: react
description: React rules for the project Applies to files matching: **/*.tsx. Use when this capability is needed.
metadata:
  author: xensen008
---

## Components

- **Prefer function components**: Use React function components with hooks over class components.
- **Client vs server**: Mark interactive components with `"use client"` at the top of the file; keep non-interactive logic in server components or libraries.
- **No default exports**: Use named exports for all components.

## Hooks

- **Hook ordering**: Follow the standard rules of hooks; no conditional or looped hooks.
- **Derived state**: Prefer deriving values from props/form state instead of duplicating them in `useState`.
- **Effects**: Keep `useEffect` minimal and side effect focused; avoid using it for basic data derivation.

## Forms

- **Validation**: Use `react-hook-form` + Zod for all non-trivial forms.
- **UI primitives**: Prefer shadcn `Form` primitives (`Form`, `FormField`, `FormItem`, `FormLabel`, `FormControl`, `FormMessage`) for form layout and error handling.
- **Schema location**: Co-locate small form schemas with the component; extract only when reused across modules.

## Styling & Layout

- **Class merging**: Use the shared `cn` utility for conditional classes.
- **Composition**: Prefer smaller composed components over deeply nested JSX in a single component.

## Configuration Access

- **Client components**: Always use `useConfig()` from `@/components/config-provider` to access site configuration in client components (`"use client"`).
- **Never import `siteConfig` directly** in client components—it derives values from server-only environment variables. The `ConfigProvider` receives a serialized version that decouples client code from server secrets.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xensen008) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
