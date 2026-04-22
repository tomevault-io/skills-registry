---
name: vite-orchestrator
description: Scaffolds Vite + React + TypeScript configuration with tsconfig aliases, Tailwind support, and recommended scripts. Use when this capability is needed.
metadata:
  author: cargdev
---

# Vite Orchestrator

When to use this skill

- Use when initializing the frontend or when a repo lacks a Vite + TS config.
- Triggered by prompts mentioning Vite, vite.config, tsconfig paths, or Tailwind wiring.

Instructions

1. First Step: Verify project root and package.json exist. If not present, suggest `npm create vite@latest -- --template react-ts`.

2. Second Step: Create/update `tsconfig.json` with `baseUrl` and `paths` (`@/*` → `src/*`). Provide a `tsconfig.json` snippet and ensure the TypeScript server is restarted.

3. Third Step: Create `vite.config.ts` using `@vitejs/plugin-react` and `vite-tsconfig-paths` and configure CSS handling (PostCSS/Tailwind if requested).

4. Fourth Step: Add recommended npm scripts to `package.json` (`dev`, `build`, `preview`, `lint`, `test`, `format`) and list required dependencies to install.

Examples

- "generate vite.config.ts with tsconfig aliases"
- Commands: `npm i -D vite @vitejs/plugin-react vite-tsconfig-paths` and `npm i -D tailwindcss postcss autoprefixer` (if Tailwind enabled)

Additional Resources

- Vite: https://vitejs.dev
- vite-tsconfig-paths: https://github.com/aleclarson/vite-tsconfig-paths

Notes

- Recommend using `vite-tsconfig-paths` to avoid duplicating aliases in Vite config.
- If running in CI, show how to set `VITE_` env vars during build.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
