---
name: stackoverflow-browser-research
description: Use browser-backed Stack Overflow adapters to inspect developer questions, problem trends, and implementation friction around technologies, products, and APIs. Use when this capability is needed.
metadata:
  author: EthanAlgoX
---

# Stack Overflow Browser Research

Use this skill when the user needs developer pain-point or implementation
signal around a technology, framework, API, or product.

## Workflow

1. Use `browser_site` with Stack Overflow adapters that exist in the runtime catalog. Prefer exact adapters such as:
   - `stackoverflow/search`
   - `stackoverflow/thread`
2. Read [references/adapter-examples.md](references/adapter-examples.md) when you need concrete adapter call patterns or fallback behavior.
3. Extract:
   - recurring implementation issues
   - setup or migration friction
   - whether interest looks broad or niche
4. Pair with `hackernews-browser-research` when developer discussion quality matters.

## Rules

- Do not invent undocumented `stackoverflow/*` adapters. If the runtime catalog does not expose the one you need, say so and continue with the closest listed adapter.
- Treat Stack Overflow as friction and adoption signal, not as direct business proof.
- Separate novice setup issues from structural product weakness.

---
> Source: [EthanAlgoX/MarketBot](https://github.com/EthanAlgoX/MarketBot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
