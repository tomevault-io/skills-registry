---
name: architecture-guidelines
description: System architecture: modules, project structure, ADRs, and testing. Use when designing or reviewing systems. Use when this capability is needed.
metadata:
  author: eser
---

# Architecture Guidelines

Guidelines for system design, project structure, and architectural decisions.

## Quick Start

```typescript
// Use ES Modules with explicit extensions
import * as path from "@std/path";
import { readFile } from "./utils.ts";

export function processFile() {}
```

## Key Principles

- Use ES Modules (avoid CommonJS/AMD)
- Follow consistent directory structure with kebab-case directories
- Document architectural decisions with ADRs including trade-offs
- Write automated tests with CI (target 80%+ coverage for critical paths)
- Use naming conventions: PascalCase for components, camelCase for utilities

## References

See [rules.md](references/rules.md) for complete guidelines with examples.

---
> Source: [eser/rules](https://github.com/eser/rules) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
