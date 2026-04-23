---
name: openwebf-app-performance-js
description: Measure and optimize WebF app performance from the JavaScript side (performance.mark/measure, bundle size, code splitting, debouncing, CSS transforms). Use when the user mentions performance.mark/measure, JS profiling, heavy JS work, bundle size, code splitting, debouncing, or animation performance. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF App: Performance (JavaScript Side)

## Instructions

1. Establish a measurement baseline (prefer production builds for accuracy).
2. Add minimal instrumentation (`performance.mark/measure`) around suspected hot paths.
3. Apply high-leverage best practices:
   - reduce sync work on critical path
   - debounce expensive operations
   - split bundles and monitor size
4. Use MCP docs for recommended practices and code snippets.

If the user’s question is primarily about host-side FP/FCP/LCP wiring or `dumpLoadingState`, prefer `openwebf-host-performance-metrics`.

More:
- [reference.md](reference.md)
- [doc-queries.md](doc-queries.md)
- [examples.md](examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
