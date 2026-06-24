---
name: app
description: Opinionated app components building on top of ./ui primitives Use when this capability is needed.
metadata:
  author: ggml-org
---

- Can include business logic and state management
- Can include data fetching and caching logic
- Should use original spelling for HTML-native events and `camelCase` for custom events
- Props and markup attributes should be listed alphabetically
- Use JS Objects and Arrays for CSS classes and styles when they are dynamic
- Whenever there can be repetition in the component's markup, if it's too small to be decoupled as a separate component — use Svelte 5's `{#snippet}` + `{@render}`

---
> Source: [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
