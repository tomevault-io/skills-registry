---
name: typescript
description: Typescript rules for the project Applies to files matching: *.tsx,*.ts. Use when this capability is needed.
metadata:
  author: xensen008
---

## Typing Guidelines

- Avoid `any` at all cost. The types should work or they indicate a problem.
- Never use `as "any"` or `as unknown as` to solve/avoid type errors. The types should work or they indicate a problem.
- Avoid using `as` to cast to a specific type. The types should work or they indicate a problem.

## Exports / Imports

- Never create index barrel files (index.ts, index.js)
- Always use direct imports with named exports
- Always use inline interfaces with function parameters

## Examples

### Good - Inline interface with function:

```typescript
export function processData({
  id,
  name,
  options,
}: {
  id: string;
  name: string;
  options: ProcessingOptions;
}): ProcessedResult {
  // implementation
}
```

### Bad - Separated interface:

```typescript
interface ProcessDataProps {
  id: string;
  name: string;
  options: ProcessingOptions;
}

export function processDAta({
  id,
  name,
  options,
}: ProcessDataProps): ProcessResult {
  // Implementation
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xensen008) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
