---
name: vite-react-ts-env
description: Add environment variables in a Vite React-TS project correctly (VITE_ prefix, typing, .env files, safe usage in React components). Use when this capability is needed.
metadata:
  author: andireuter
---

# Vite Env Variables (React-TS) Skill

Implement environment variables correctly in Vite.

## Rules / best practices

1) Client-exposed env vars MUST be prefixed with VITE_.
2) Access via import.meta.env (not process.env).
3) Document variables in .env.example.
4) Do not commit secrets; if asked, recommend server-side storage instead.
5) Provide TypeScript types for env when helpful (vite-env.d.ts).

## Deliverables

- .env.example entries
- Optional: env typing augmentation
- Usage snippet in a React component

Use template.md as the default structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andireuter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
