---
name: venuscn-repo
description: Use when working in a repository that uses the VenusCN component library, or when building React and Next.js interfaces that should match the Contentstack Venus visual language. Helps agents orient quickly, find the right source files, prefer existing components over ad hoc UI, use the demo app as a reference implementation, and run the correct validation commands.
metadata:
  author: lytics
---

# VenusCN Repo Skill

Use this skill when the task involves building, editing, or reviewing interfaces in a repo that contains `@contentstack/venuscn` or follows the same Venus-inspired design language.

## Quick Start

Read these files first:

1. `README.md`
2. `packages/venuscn/README.md`
3. `apps/demo/HOW_THE_DEMO_WORKS.md`
4. `docs/guides/design-system.md`

If the task is still unclear after that, read `references/repo-map.md`.

## Core Working Model

- Treat `packages/venuscn` as the reusable design-system package.
- Treat `apps/demo` as the visual reference implementation and pattern library.
- Prefer existing VenusCN components before creating new app-specific UI.
- Reach for shadcn/ui app components only when the reusable package does not cover the need.
- Match existing spacing, typography, borders, and page structure before inventing new patterns.

## Build UI The VenusCN Way

For interface work, follow this order:

1. Find the closest matching demo page or gallery example.
2. Reuse an existing `@contentstack/venuscn` component if one exists.
3. Compose with existing tokens and layout patterns.
4. Add a new reusable component only if the pattern is clearly repeated or design-system-worthy.

Use `references/interface-patterns.md` when you need a fast pattern map.

## Editing Rules

- Preserve the repo's existing visual language.
- Keep reusable logic in `packages/venuscn` and app-specific behavior in `apps/demo`.
- Prefer token-based colors and spacing over one-off values unless the repo already uses a hardcoded value for that exact pattern.
- When changing docs or repo behavior, update linked documentation in `docs/` and the root `README.md` if needed.

## Validation

Default validation command after code changes:

```bash
pnpm check
```

If only docs changed, validate links and docs references instead of running the full code pipeline.

## When To Read More

- Read `references/repo-map.md` for package and file ownership.
- Read `references/interface-patterns.md` for common UI composition patterns in this repo.

---
> Source: [lytics/venus](https://github.com/lytics/venus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
