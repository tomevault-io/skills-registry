---
name: vue-debug-guides
description: Vue debugging guidance for diagnosing runtime errors, warnings, SSR and hydration mismatches, reactivity issues, and component failures. Use when fixing Vue bugs, investigating warnings, or tracing broken behavior in Vue 3 applications. Use when this capability is needed.
metadata:
  author: HsinPu
---

# Vue Debug Guides

Use this skill when Vue behavior is broken or suspicious.

## Workflow

1. Reproduce the issue and capture the exact warning, stack trace, or failing interaction.
2. Separate render, state, lifecycle, router, and SSR/hydration causes.
3. Check the smallest component or composable that can explain the failure.
4. Confirm the fix removes the symptom without introducing a new reactivity or lifecycle bug.

## Rules

- Start from the console message, not from a guess.
- Check props, emits, watchers, computed values, and lifecycle order first.
- Treat hydration mismatches and async race conditions as first-class causes.
- Keep the diagnosis narrow and behavior-focused.

## Handoff

- For general Vue architecture, use `vue-development`.
- For Nuxt SSR and hydration issues, use `nuxt-development`.
- For browser-level verification, use `webapp-testing`.

---
> Source: [HsinPu/Autoverse-Ai-Agent-Skills](https://github.com/HsinPu/Autoverse-Ai-Agent-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->
