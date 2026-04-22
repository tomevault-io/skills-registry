---
name: javascript-best-practices
description: Idiomatic JavaScript patterns and conventions for maintainable code. Use when this capability is needed.
metadata:
  author: hoangnguyen0403
---

# JavaScript Best Practices

## **Priority: P1 (OPERATIONAL)**

Conventions and patterns for writing maintainable JavaScript.

## Implementation Guidelines

- **Naming**: `camelCase` (vars/funcs), `PascalCase` (classes), `UPPER_SNAKE` (constants).
- **Errors**: Throw `Error` objects only. Handle all async errors.
- **Comments**: JSDoc for APIs. Explain "why" not "what".
- **Files**: One entity per file. `index.js` for exports.
- **Modules**: Named exports only. Order: Ext -> Int -> Rel.

## Anti-Patterns

- **No Globals**: Encapsulate state.
- **No Magic Numbers**: Use `const`.
- **No Nesting**: Guard clauses/early returns.
- **No Defaults**: Use named exports.
- **No Side Effects**: Keep functions pure.

## Code

```javascript
// Constants
const STATUS = { OK: 200, ERROR: 500 };

// Errors
class APIError extends Error {
  constructor(msg, code) {
    super(msg);
    this.code = code;
  }
}

// Async + JDoc
/** @throws {APIError} */
export async function getData(id) {
  if (!id) throw new APIError('Missing ID', 400);
  const res = await fetch(`/api/${id}`);
  if (!res.ok) throw new APIError('Failed', res.status);
  return res.json();
}
```

## Reference & Examples

For module patterns and project structure:
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

language | tooling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoangnguyen0403) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
