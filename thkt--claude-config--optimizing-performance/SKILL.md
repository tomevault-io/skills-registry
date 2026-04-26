---
name: optimizing-performance
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Performance Optimization

## Core Web Vitals

| Metric | Target | Measure                  |
| ------ | ------ | ------------------------ |
| LCP    | <2.5s  | Largest Contentful Paint |
| FID    | <100ms | First Input Delay        |
| CLS    | <0.1   | Cumulative Layout Shift  |

## Workflow

1. Measure (Lighthouse/DevTools)
2. Identify bottleneck
3. One change at a time
4. Re-measure

## References

| Topic  | File                                                    |
| ------ | ------------------------------------------------------- |
| Vitals | `${CLAUDE_SKILL_DIR}/references/web-vitals.md`          |
| React  | `${CLAUDE_SKILL_DIR}/references/react-optimization.md`  |
| Bundle | `${CLAUDE_SKILL_DIR}/references/bundle-optimization.md` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
