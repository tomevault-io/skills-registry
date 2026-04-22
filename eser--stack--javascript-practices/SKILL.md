---
name: javascript-practices
description: JavaScript/TypeScript conventions: ES modules, Deno runtime, React patterns, async/await, and type handling. Use when writing .ts, .tsx, or .js files, configuring imports, or building React components. Use when this capability is needed.
metadata:
  author: eser
---

# JavaScript/TypeScript Practices

Conventions for JS/TS syntax, modules, types, and runtime behavior.

## Quick Start

```typescript
import * as path from "@std/path"; // namespace import
import { utils } from "./utils.ts"; // explicit extension

export function buildConfig() {} // direct named export
const port = config.port ?? 8000; // nullish coalescing
```

## Key Principles

**Modules:** Direct named exports, namespace imports, explicit `.ts` extensions,
avoid `export *` re-exports (use direct imports from specific modules)

**Syntax:** `const` over `let`, always semicolons, `===` strict equality, `??`
over `||`

**Types:** `Number()` over `+`, `instanceof` over `typeof`, prefer `null` over
`undefined`

**Runtime:** `import.meta.dirname`, `globalThis` over `window`, optional
`projectRoot` params

**Async:** Use `return await` consistently for better stack traces and correct
error handling

**Deno:** Scripts in `package.json` (not deno.json tasks), use package aliases
directly, no wrapper scripts for built-in commands

**Avoid:** `eval`, prototype mutation, truthy/falsy checks on non-booleans

## References

See [rules.md](references/rules.md) for complete guidelines with examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
