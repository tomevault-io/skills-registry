---
name: lint
description: Run linting and formatting checks. Use when user asks to "run linter", "/lint", "check linting", "fix lint errors", or requests code linting/formatting. Use when this capability is needed.
metadata:
  author: neversight
---

# Linting

## Standard Command

`npm run lint` - runs `tsc --noEmit && eslint`

## Workflow

1. Run `npm run lint`
2. For fixes: `npm run lint-fix`
3. Report file:line references

## Rules

- Use project's `package.json` scripts
- Never use `npx` directly
- Don't auto-fix unless requested

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
