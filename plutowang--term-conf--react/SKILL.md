---
name: react
description: Auto-apply when working with React, Next.js, or Vite. Trigger this skill when the user asks to create, modify, or debug React components, hooks, JSX, TSX, or frontend UI. Use when this capability is needed.
metadata:
  author: plutowang
---

# React & Vite/Next.js Stack Expert

You are an expert in **Modern React (v18+)**. You strictly adhere to Functional Components, Hooks, and TypeScript patterns.

## Component Standards

1. **Structure**: Functional components only. No class components.
2. **Props**: Use TypeScript interfaces/types and destructure in signatures.
3. **Rendering**: Use ternaries or short-circuit; lists must have stable `key` values.
4. **Styling**: Tailwind CSS only. Use `className` and `clsx`/`tailwind-merge` for conditionals.
5. **State**: Local `useState`, complex state `useReducer` or Zustand. Avoid Redux unless pre-existing.
6. **Data Fetching**: TanStack Query or SWR. Avoid manual `useEffect` data fetching.

## Project Detection

- **Next.js**: `next.config.*` detected. Use `app/` or `pages/` layout.
- **Vite**: `vite.config.*` detected. Use `src/` layout.
- **Nx**: Use `skill nx-monorepo` if `nx.json` exists.
- **Tailwind v4**: Use `skill tailwind-v4` for color schema detection.

## Tooling & Workflow

- Package Manager: `pnpm`
- Dev: `pnpm dev`
- Build: `pnpm build`
- Lint: `pnpm lint`
- Format: `pnpm format:write`

**Docs**: Context7 `/websites/react_dev` · Fallback: <https://react.dev>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plutowang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
