---
name: react-frontend
description: > Use when this capability is needed.
metadata:
  author: razobeckett
---

# React Frontend Development

> Vite + React 19 + TypeScript + TanStack Query + Zustand + shadcn/ui

## Quick Reference

| Layer | Tool | Purpose |
|-------|------|---------|
| Framework | React 19 + Vite | UI rendering + dev server |
| Styling | Tailwind CSS + shadcn/ui | Utility-first CSS + components |
| Server State | TanStack Query + Axios | Fetch, cache, sync server data |
| UI State | Zustand | Client-only state (dialogs, filters) |
| Icons | lucide-animated > lucide-react | Prefer animated variants |
| Animation | Motion (framer-motion) | Shared presets only |
| Lint/Format | Biome | Single tool for both |

## Critical Rules

### ALWAYS
- TypeScript strict mode (no `any`, no `@ts-ignore`)
- Prefer `bun` package manager, fallback to `pnpm` (NEVER npm)
- Use Biome for linting AND formatting
- TanStack Query for ALL server data
- Zustand for UI-only state
- lucide-animated icons when available

### NEVER
- Suppress type errors (`as any`, `@ts-ignore`, `@ts-expect-error`)
- Use npm as package manager
- Mix server state in Zustand
- Custom animations outside shared presets
- Paste secrets or production data in code

### ASK FIRST
- Auth implementation (WorkOS vs better-auth)
- Major refactors affecting 3+ files
- Changing core dependencies (icons, auth, state management)
- Non-obvious architectural decisions

## State Management Decision Tree

```
Is it from an API/server?
├─ YES → TanStack Query (useQuery, useMutation)
└─ NO → Is it UI-related?
         ├─ YES → Zustand (dialogs, selections, filters)
         └─ NO → Consider if state is needed at all
```

## Commands

```bash
# Setup (use bun, fallback pnpm)
bun install        # or: pnpm install

# Development
bun run dev        # or: pnpm dev

# Build
bun run build      # or: pnpm build

# Lint & Format
bun run check      # biome check
bun run format     # biome format --write
```

## Detailed References

| Topic | File |
|-------|------|
| Tech stack configuration | [references/stack.md](references/stack.md) |
| Code patterns & conventions | [references/patterns.md](references/patterns.md) |
| Agentic workflow rules | [references/workflow.md](references/workflow.md) |
| Project structure | [references/structure.md](references/structure.md) |

## Quality Gates (Pre-commit)

Required before any commit:
1. `biome check` - Lint + format validation
2. `tsc --noEmit` - TypeScript type checking

Husky + lint-staged enforces these automatically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/razobeckett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
