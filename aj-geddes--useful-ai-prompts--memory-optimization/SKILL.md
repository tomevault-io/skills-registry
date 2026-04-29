---
name: memory-optimization
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# Memory Optimization

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Memory optimization improves application performance, stability, and reduces infrastructure costs. Efficient memory usage is critical for scalability.

## When to Use

- High memory usage
- Memory leaks suspected
- Slow performance
- Out of memory crashes
- Scaling challenges

## Quick Start

Minimal working example:

```javascript
// Browser memory profiling

// Check memory usage
performance.memory: {
  jsHeapSizeLimit: 2190000000,    // Max available
  totalJSHeapSize: 1300000000,    // Total allocated
  usedJSHeapSize: 950000000       // Currently used
}

// React DevTools Profiler
- Open React DevTools → Profiler
- Record interaction
- See component renders and time
- Identify unnecessary renders

// Chrome DevTools
1. Open DevTools → Memory
2. Take heap snapshot
3. Compare before/after
4. Look for retained objects
5. Check retained sizes

// Node.js profiling
node --inspect app.js
// Open chrome://inspect
// ... (see reference guides for full implementation)
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [Memory Profiling](references/memory-profiling.md) | Memory Profiling |
| [Memory Leak Detection](references/memory-leak-detection.md) | Memory Leak Detection |
| [Optimization Techniques](references/optimization-techniques.md) | Optimization Techniques |
| [Monitoring & Targets](references/monitoring-targets.md) | Monitoring & Targets |

## Best Practices

### ✅ DO

- Follow established patterns and conventions
- Write clean, maintainable code
- Add appropriate documentation
- Test thoroughly before deploying

### ❌ DON'T

- Skip testing or validation
- Ignore error handling
- Hard-code configuration values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
