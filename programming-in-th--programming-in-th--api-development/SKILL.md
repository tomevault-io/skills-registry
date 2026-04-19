---
name: api-development
description: Use when creating or modifying Elysia API routes. Ensures proper validation with t schema, auth guards, error handling, and performance patterns.
metadata:
  author: programming-in-th
---

See [API patterns](../../docs/api-patterns.md) for full reference.

**Quick example**:
```typescript
import { Elysia, t } from 'elysia'
import { prisma } from '@/lib/prisma'

new Elysia()
  .get('/tasks/:id', async ({ params, status }) => {
    const task = await prisma.task.findUnique({
      where: { id: params.id },
      select: { id: true, title: true }
    })
    if (!task) return status(404, { error: 'Not found' })
    return task
  }, {
    params: t.Object({ id: t.String() })
  })
```

**Checklist**: `t.Object` validation, auth guards, selective Prisma fields, pagination, proper status codes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/programming-in-th) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
