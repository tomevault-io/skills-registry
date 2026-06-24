---
name: api-scaffolder
description: Scaffold REST API endpoints following project conventions. Use when creating new routes, controllers, or CRUD endpoints in an Express/Node.js project. Use when this capability is needed.
metadata:
  author: ossamanahle
---

When this skill is active, you are a senior API developer scaffolding endpoints that follow this project's conventions exactly.

## Conventions

### Route file structure
Every endpoint lives at `src/api/routes/<resource>/<action>.ts` and exports a single route handler:

```typescript
import { Router } from 'express';
import { validate } from '../../middleware/validate';
import { <Resource>Schema } from './schemas';

const router = Router();

router.<method>('/', validate(<Resource>Schema), async (req, res, next) => {
  try {
    // handler logic
    res.status(<code>).json({ data: result });
  } catch (err) {
    next(err);
  }
});

export default router;
```

### Validation
- Use Zod schemas in a sibling `schemas.ts` file
- Every input field must have a schema — no unvalidated request bodies
- Return 400 with `{ error: { code: "VALIDATION_ERROR", details: [...] } }` on failure

### Error response format
All errors follow this shape:
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "User with id 42 not found",
    "details": []
  }
}
```

Standard codes: `VALIDATION_ERROR`, `RESOURCE_NOT_FOUND`, `DUPLICATE_RESOURCE`, `UNAUTHORIZED`, `INTERNAL_ERROR`.

### Test file structure
Every route gets a test at `src/api/tests/<resource>/<action>.test.ts`:
- At least 1 happy-path test
- At least 1 validation-failure test (400)
- At least 1 not-found test (404) for read/update/delete operations

### Naming conventions
- Resource names: singular, lowercase (`user`, `order`, `product`)
- File names: `create.ts`, `read.ts`, `update.ts`, `delete.ts`, `list.ts`
- Schema names: `Create<Resource>Schema`, `Update<Resource>Schema`, `<Resource>Params`

## Rules
- Always generate the route handler, the Zod schema, and the test file together — never one without the others
- Register the new route in `src/api/routes/index.ts`
- Use the project's existing database client (`src/db/client.ts`) — do not create new connections
- Wrap all async handlers in try/catch with `next(err)` — never let promises reject silently
- Return 201 for successful creates, 200 for reads/updates, 204 for deletes

## Output Format
For each endpoint scaffolded, report:
- Files created (with full paths)
- Route registered at (method + path)
- Schema fields defined
- Tests generated (count and names)

## What to Avoid
- Do not modify existing route files — only create new ones
- Do not change the database schema or run migrations
- Do not install new dependencies without asking first
- Do not create middleware — use existing middleware from `src/api/middleware/`
- Do not scaffold more endpoints than requested

---
> Source: [ossamanahle/claude-code-wizard](https://github.com/ossamanahle/claude-code-wizard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
