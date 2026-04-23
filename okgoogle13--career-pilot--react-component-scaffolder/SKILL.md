---
name: react-component-scaffolder
description: DEPRECATED: Use component-builder instead for M3-compliant, production-grade components. Use when this capability is needed.
metadata:
  author: okgoogle13
---

# React Component Scaffolder (DEPRECATED)

⚠️ **This skill is deprecated and should no longer be used.**

## Why Deprecated?

The `component-builder` skill is the recommended replacement. It provides:

- **Production-grade** components with best practices
- **M3 Expressive** design token compliance
- **Token-aware** styling (no hardcoded values)
- **Accessibility-first** approach with ARIA labels
- **Comprehensive** error handling and edge cases
- **TypeScript** interfaces with JSDoc documentation

## Migration Path

When asked to "create a component", use **`component-builder`** instead:

**Old way:**

```
User: Create a Button component
Assistant: Uses react-component-scaffolder
```

**New way:**

```
User: Create a Button component
Assistant: Uses component-builder (M3-compliant)
```

## Why Not Use This Skill Anymore?

1. `react-component-scaffolder` only creates directory structure with no intelligent code generation
2. `component-builder` generates complete, production-ready components with M3 tokens
3. Having both creates user confusion about which to use
4. `component-builder` is strictly superior in every way

See [`component-builder`](../component-builder/SKILL.md) for the recommended replacement.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/okgoogle13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
