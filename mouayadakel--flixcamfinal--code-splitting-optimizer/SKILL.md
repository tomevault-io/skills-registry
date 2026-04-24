---
name: code-splitting-optimizer
description: Suggests dynamic imports and route-level splitting for large components. Use when large components (>100KB) or heavy deps are detected. Use when this capability is needed.
metadata:
  author: mouayadakel
---

# Code Splitting Optimizer

## When to Trigger

- Large components detected (>100 KB)
- Route-level bundling
- "Optimize loading"

## What to Do

1. **Heavy components**: Wrap in next/dynamic with loading fallback; use ssr: false for client-only (e.g. charts).
2. **Routes**: Rely on App Router automatic route splitting; avoid importing heavy modules from layout.
3. **Vendor**: Use splitChunks in next.config if needed (e.g. framework, UI lib, charts in separate chunks).
4. **Conditional**: Load modals, tabs, or below-fold sections only when needed (dynamic import on open or inView).

Keep initial bundle small; measure before/after. Don’t over-split tiny components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
