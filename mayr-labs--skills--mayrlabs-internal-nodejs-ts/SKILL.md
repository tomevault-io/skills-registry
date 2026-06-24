---
name: mayrlabs-internal-nodejs-ts
description: Extracted Node.js / TypeScript standards from MayR Labs GEMINI.md Use when this capability is needed.
metadata:
  author: MayR-Labs
---

# MayR Labs Internal: Node.js / TypeScript Standards

- TypeScript is **not optional**
- Avoid `any` unless absolutely necessary (and justified)

## Structure

- Use layered architecture:
  - Controllers
  - Services
  - Repositories

## Errors

- Never silently fail
- Always:
  - log errors
  - return meaningful responses

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
