---
name: dev-typescript-backend-development
description: TypeScript / Node.js backend service development skill. Covers Express / Fastify patterns, Zod runtime validation, async DB access (Prisma / Drizzle / Kysely), middleware chains, dependency injection, structured config, and observability. Pairs with typescript.instructions.md. Use when this capability is needed.
metadata:
  author: OriPoin
---

# TypeScript / Node.js Backend Development

## When to Use

- Writing or reviewing TypeScript / Node.js web services / REST APIs / async workers.
- Pairs with [typescript.instructions.md](../../instructions/typescript.instructions.md).
- For React/TSX frontend work see [dev-react-frontend-development](../dev-react-frontend-development/SKILL.md).
- Collaboration protocol: see [ops-agent-collaboration](../ops-agent-collaboration/SKILL.md).

## Golden Rules

1. **Validate at the boundary, trust the inside**: Zod schemas on every request body / query / header; service layer takes typed values only.
2. **Async end-to-end**: `async`/`await` through the stack; every Promise is `await`ed or has an explicit `.catch`; no callback hell.
3. **Dependency injection over global singletons**: pass dependencies as constructor args or via a DI container; no module-level singletons.
4. **One transaction per request** (or per use-case); never auto-commit in handlers.
5. **Domain errors are typed**: a small class hierarchy mapped at the framework boundary to HTTP status codes.
6. **Configuration via typed env parsing**: `zod` or `envalid` for typed env vars; exit immediately on validation failure.
7. **Observability default-on**: structured logging (pino/winston), correlation IDs (`requestId` / `traceId`), request timing middleware.
8. **No N+1**: explicit eager loading in ORM queries; add a debug-time query counter.
9. **Bounded concurrency on outbound calls**: timeouts via `AbortController`, retries with jitter for transient failures only.
10. **Migrations are versioned**: Prisma migrate / Drizzle Kit; one head per branch; reviewed alongside the code that needs them.

## Recipes

### Recipe A: Express handler with Zod + typed error

```typescript
import { Router, Request, Response, NextFunction } from 'express';
import { z } from 'zod';

const CreateOrderSchema = z.object({
  userId: z.string().min(1),
  amountCents: z.number().int().positive(),
});

type CreateOrderInput = z.infer<typeof CreateOrderSchema>;

const router = Router();

router.post('/orders', async (req: Request, res: Response, next: NextFunction) => {
  try {
    const input = CreateOrderSchema.parse(req.body);
    const order = await orderService.create(input);
    res.status(201).json(order);
  } catch (err) {
    if (err instanceof z.ZodError) {
      res.status(400).json({ error: err.errors });
    } else if (err instanceof UserNotFoundError) {
      res.status(404).json({ error: err.message });
    } else {
      next(err);
    }
  }
});
```

### Recipe B: Prisma transaction per request

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function createOrder(input: CreateOrderInput): Promise<Order> {
  return prisma.$transaction(async (tx) => {
    const user = await tx.user.findUnique({ where: { id: input.userId } });
    if (!user) throw new UserNotFoundError(input.userId);

    return tx.order.create({
      data: { userId: input.userId, amountCents: input.amountCents },
    });
  });
}
```

### Recipe C: Typed environment config

```typescript
import { z } from 'zod';

const EnvSchema = z.object({
  DATABASE_URL: z.string().url(),
  LOG_LEVEL: z.enum(['trace', 'debug', 'info', 'warn', 'error', 'fatal']).default('info'),
  REQUEST_TIMEOUT_MS: z.coerce.number().int().positive().default(2000),
});

const env = EnvSchema.parse(process.env); // exits on validation failure
```

### Recipe D: Outbound call with AbortController timeout

```typescript
import fetch from 'node-fetch';

async function callExternalAPI(url: string, timeoutMs: number = 2000): Promise<Response> {
  const controller = new AbortController();
  const timeout = setTimeout(() => controller.abort(), timeoutMs);

  try {
    return await fetch(url, { signal: controller.signal });
  } finally {
    clearTimeout(timeout);
  }
}
```

### Recipe E: Structured logging middleware

```typescript
import pino from 'pino';
import { randomUUID } from 'crypto';

const logger = pino({ level: env.LOG_LEVEL });

app.use((req, res, next) => {
  const requestId = req.headers['x-request-id'] as string || randomUUID();
  req.headers['x-request-id'] = requestId;

  const childLogger = logger.child({ requestId });
  req.log = childLogger;

  const start = Date.now();
  res.on('finish', () => {
    childLogger.info({
      method: req.method,
      url: req.url,
      statusCode: res.statusCode,
      latencyMs: Date.now() - start,
    });
  });

  next();
});
```

## Anti-patterns

### [BAD] Mixing ESM and CJS
Fix: pick one module system in `tsconfig.json` and `package.json`; do not mix.

### [BAD] Using `any` or `as` to bypass type checks
Fix: use proper Zod runtime validation at boundaries; use discriminated unions internally.

### [BAD] Calling `console.log` in production
Fix: use the project's unified logging facade (pino/winston).

### [BAD] Global module-level singletons
Fix: inject dependencies via constructor or DI container.

### [BAD] Silent catch blocks
Fix: after catching, decide: convert, log and continue, or rethrow.

### [BAD] Unbounded `fetch` / `axios` calls without timeout
Fix: always use `AbortController` or equivalent.

## Review Checklist

- Is `strict` enabled in tsconfig?
- Are all route handlers validated with Zod at the boundary?
- Is `async`/`await` used end-to-end?
- Are domain errors typed and mapped to HTTP status codes?
- Is structured logging used instead of `console.log`?
- Are outbound calls bounded by timeouts?
- Are database queries free of N+1?
- Is configuration validated at startup?

---
> Source: [OriPoin/AgentOrch](https://github.com/OriPoin/AgentOrch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
