---
name: testing-patterns
description: Project test patterns. Applies when writing tests or mocking Convex. Use when this capability is needed.
metadata:
  author: jasondocton
---

# Testing

- Framework: Vitest + `bun test`
- Files: `*.test.ts` colocated with source
- Convex: use `convex-test` for backend tests
- Coverage: `bun test --coverage`

## Convex Mocking

```ts
import { convexTest } from "convex-test"
import schema from "./schema"

const t = convexTest(schema)
await t.run(async (ctx) => {
  // test mutations/queries here
})
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jasondocton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
