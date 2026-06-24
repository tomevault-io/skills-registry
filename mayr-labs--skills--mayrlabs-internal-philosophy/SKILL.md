---
name: mayrlabs-internal-philosophy
description: Extracted Philosophy and Anti-Patterns from MayR Labs GEMINI.md Use when this capability is needed.
metadata:
  author: MayR-Labs
---

# MayR Labs Internal: General Philosophy & Anti-Patterns

## General Philosophy

- Code is written for **humans first, machines second**
- Optimise for **readability, maintainability, and scalability**
- Avoid “clever” code — prefer **boring, predictable patterns**
- Every decision should answer: _“Will this still make sense in 6 months?”_
- Clean Code, Lean Code
- Let the code breathe, **do not cluster unrelated lines of code together**

## Project Standards (All Stacks)

- Codebases must follow **clear separation of concerns**
- Avoid monolithic files — prefer **modular architecture**
- Co-locate related logic where appropriate, but avoid tight coupling
- Use **descriptive, intention-revealing names**
- No abbreviations unless universally understood (`id`, `url`)
- Every project using `.env` **must include `.env.example`**
- Tooling for JS/TS: `npm run lint`, `npm run format`, `npm run type-check`

## Anti-Patterns (BANNED)

- God files (1000+ lines doing everything)
- Hardcoded configs
- Business logic inside UI components
- Silent error handling
- Copy-paste programming

## Final Rule

> If something feels messy, it probably is. Fix it immediately.

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
