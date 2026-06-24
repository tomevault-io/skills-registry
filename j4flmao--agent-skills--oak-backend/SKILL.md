---
name: oak-backend
description: > Use when this capability is needed.
metadata:
  author: j4flmao
---

# Oak Backend

## Purpose
Define Oak backend application architecture: Deno-native HTTP server, middleware composition, router pattern, and context management with typed state, composable middleware pipeline, and type-safe routing.

## Agent Protocol

### Trigger
User request includes: `oak`, `oak backend`, `deno oak`, `oak middleware`, `oak router`, `deno http`, `oak context`, `oak typescript`.

### Input Context
- Deno version (1.40+)
- Oak version (13.x)
- Language (TypeScript)
- Database (Deno KV, MongoDB via deno_mongo, PostgreSQL via deno_postgres)
- Templating (eta, deno mustache)
- Deployment (Deno Deploy, self-hosted)

### Output Artifact
A markdown document containing:
- Project structure
- Router setup (Router class)
- Middleware composition (use, compose)
- Context (c.state, c.cookies, c.send)
- Error handling middleware
- State management (context state)
- Environment configuration (Deno.env)
- Testing (Deno.test, superdeno)

### Response Format
Produce the artifact directly. No preamble, no postamble, no explanations. No filler, no hedging. Compress output.

### Completion Criteria
- Router separates route definitions from app setup
- Middleware pipeline composed with app.use()
- Error middleware catches all exceptions
- State typed via OakMiddleware type parameter
- Tests use superdeno or fetch-based requests
- Lockfile generated and committed

### Max Response Length
4096 tokens

## Architecture Decision Trees

### Oak vs Hono vs std/http

| Criterion | Oak | Hono | std/http |
|-----------|-----|------|----------|
| Middleware ecosystem | Rich (cors, auth, rate-limit, static) | Growing (cors, etag, jwt) | Manual |
| TypeScript context typing | Generic `State` param | `c.req.valid()` | Manual |
| Router pattern | `Router({ prefix })` | Chained `.get().post()` | Switch/match |
| File upload | `ctx.request.body({ type: 'form-data' })` | `c.req.parseBody()` | Manual |
| WebSocket | `oak_websocket` | Built-in `hono/ws` | Manual |
| Community | Mature (12.x, stable) | Growing (4.x) | Std lib |

Decision: Complex middleware needs → Oak. TypeScript validation-first → Hono. Max throughput/minimal → std/http.

### Route Organization Strategy

| Scale | Pattern | Structure |
|-------|---------|-----------|
| Small (<10 routes) | Single router file | `src/router.ts` |
| Medium (10-50 routes) | Resource modules | `src/routes/{resource}.ts` |
| Large (50+ routes) | Versioned sub-routers | `src/routes/v1/{resource}.ts` |
| Micro (3-5 routes) | Inline in app.ts | `app.use(router.routes())` |

## Workflow

### Step 1: Project Setup
```bash
# Create project directory
mkdir -p order-service/src
cd order-service

# Initialize
deno init

# Add oak dependency (import_map.json or deno.json)
```

### Step 2: Project Structure
```
order-service/
├── src/
│   ├── main.ts
│   ├── app.ts
│   ├── router/
│   │   ├── index.ts
│   │   ├── orders.ts
│   │   └── health.ts
│   ├── middleware/
│   │   ├── error.ts
│   │   ├── logger.ts
│   │   └── auth.ts
│   ├── controllers/
│   │   └── order.controller.ts
│   ├── services/
│   │   └── order.service.ts
│   ├── models/
│   │   └── order.ts
│   └── types/
│       └── index.ts
├── tests/
│   ├── app_test.ts
│   └── orders_test.ts
├── deno.json
└── import_map.json
```

### Step 3: App Initialization
```typescript
// src/app.ts
import { Application } from 'oak'
import { errorMiddleware } from './middleware/error.ts'
import { loggerMiddleware } from './middleware/logger.ts'
import { router } from './router/index.ts'

export function createApp(): Application {
  const app = new Application()

  // State
  app.state.startTime = Date.now()

  // Middleware pipeline (order matters)
  app.use(errorMiddleware)
  app.use(loggerMiddleware)
  app.use(router.routes())
  app.use(router.allowedMethods())

  return app
}

// src/main.ts
import { createApp } from './app.ts'

const app = createApp()
const port = parseInt(Deno.env.get('PORT') ?? '8080')
console.log(`Server running on port ${port}`)
await app.listen({ port })
```

### Step 4: Router and Controller
```typescript
// src/router/orders.ts
import { Router } from 'oak'
import { OrderController } from '../controllers/order.controller.ts'

const router = new Router({ prefix: '/api/orders' })
const controller = new OrderController()

router.get('/', controller.list)
router.get('/:id', controller.getById)
router.post('/', controller.create)
router.put('/:id', controller.update)
router.delete('/:id', controller.deleteById)

export { router as orderRouter }

// src/controllers/order.controller.ts
import type { RouterContext } from 'oak'
import * as orderService from '../services/order.service.ts'

export class OrderController {
  async list(ctx: RouterContext<'/'>) {
    const orders = await orderService.findAll()
    ctx.response.body = orders
  }

  async getById(ctx: RouterContext<'/:id'>) {
    const id = ctx.params.id!
    const order = await orderService.findById(id)
    if (!order) {
      ctx.response.status = 404
      ctx.response.body = { error: 'Order not found' }
      return
    }
    ctx.response.body = order
  }

  async create(ctx: RouterContext<'/'>) {
    const body = await ctx.request.body().value
    const order = await orderService.create(body)
    ctx.response.status = 201
    ctx.response.body = order
  }
}
```

### Step 5: Middleware Pipeline
```typescript
// src/middleware/error.ts
import type { Middleware } from 'oak'

export const errorMiddleware: Middleware = async (ctx, next) => {
  try {
    await next()
  } catch (err) {
    ctx.response.status = err.status ?? 500
    ctx.response.body = {
      success: false,
      error: {
        code: err.code ?? 'INTERNAL_ERROR',
        message: err.message ?? 'Unexpected error',
      },
    }
    ctx.response.type = 'json'
  }
}

// src/middleware/logger.ts
import type { Middleware } from 'oak'

export const loggerMiddleware: Middleware = async (ctx, next) => {
  const start = Date.now()
  await next()
  const ms = Date.now() - start
  console.log(`${ctx.request.method} ${ctx.request.url.pathname} - ${ms}ms`)
}
```

### Step 6: Typed State Management

```typescript
// src/types/index.ts
export interface AppState {
  userId: string
  role: 'admin' | 'user'
  requestId: string
}

// src/middleware/auth.ts
import type { Middleware } from 'oak'
import type { AppState } from '../types/index.ts'

export const authMiddleware: Middleware<AppState> = async (ctx, next) => {
  const token = ctx.request.headers.get('Authorization')?.slice(7)
  if (!token) {
    ctx.response.status = 401
    ctx.response.body = { error: 'Unauthorized' }
    return
  }
  const payload = await verifyJwt(token)
  ctx.state.userId = payload.sub
  ctx.state.role = payload.role
  ctx.state.requestId = crypto.randomUUID()
  await next()
}
```

### Step 7: Validation with Zod

```typescript
// src/middleware/validate.ts
import { z } from 'zod/mod.ts'
import type { Middleware } from 'oak'

export function validate(schema: z.ZodSchema): Middleware {
  return async (ctx, next) => {
    const body = ctx.request.body().value
    const result = schema.safeParse(body)
    if (!result.success) {
      ctx.response.status = 400
      ctx.response.body = {
        success: false,
        error: {
          code: 'VALIDATION_ERROR',
          details: result.error.issues,
        },
      }
      return
    }
    ctx.state.validatedBody = result.data
    await next()
  }
}
```

### Step 8: Testing
```typescript
// tests/orders_test.ts
import { createApp } from '../src/app.ts'
import { assertEquals, assertExists } from 'std/testing/asserts.ts'

Deno.test('POST /api/orders creates order', async () => {
  const app = createApp()
  const listener = app.listen({ port: 0 })

  const port = (await listener).port
  const res = await fetch(`http://localhost:${port}/api/orders`, {
    method: 'POST',
    body: JSON.stringify(validPayload),
    headers: { 'Content-Type': 'application/json' },
  })
  assertEquals(res.status, 201)

  listener.close()
})

Deno.test('GET /api/orders/:id returns 404 for missing', async () => {
  const app = createApp()
  const listener = app.listen({ port: 0 })
  const { port } = await listener
  const res = await fetch(`http://localhost:${port}/api/orders/nonexistent`)
  assertEquals(res.status, 404)
  listener.close()
})
```

## Implementation Patterns

### Pattern: Composite Router (Versioned API)

```typescript
// src/router/index.ts
import { Router } from 'oak'
import { orderRouter } from './orders.ts'
import { userRouter } from './users.ts'
import { healthRouter } from './health.ts'

const api = new Router({ prefix: '/api/v1' })
api.use('/orders', orderRouter.routes(), orderRouter.allowedMethods())
api.use('/users', userRouter.routes(), userRouter.allowedMethods())

const router = new Router()
router.use('/health', healthRouter.routes(), healthRouter.allowedMethods())
router.use('/api/v1', api.routes(), api.allowedMethods())

export { router }
```

### Pattern: Static File Serving

```typescript
import { send } from 'oak/send.ts'

// Serve static files
router.get('/static/:path+', async (ctx) => {
  await send(ctx, ctx.params.path!, {
    root: `${Deno.cwd()}/public`,
    maxage: 86400,
  })
})
```

### Pattern: CORS with Dynamic Origins

```typescript
const allowedOrigins = ['https://app.example.com', 'https://admin.example.com']

app.use(async (ctx, next) => {
  const origin = ctx.request.headers.get('Origin')
  if (origin && allowedOrigins.includes(origin)) {
    ctx.response.headers.set('Access-Control-Allow-Origin', origin)
    ctx.response.headers.set('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE')
    ctx.response.headers.set('Access-Control-Allow-Headers', 'Content-Type, Authorization')
  }
  if (ctx.request.method === 'OPTIONS') {
    ctx.response.status = 204
    return
  }
  await next()
})
```

## Production Considerations

### Error Visibility
- Log all 5xx errors with full stack traces to stderr
- Use `ctx.response.type = 'json'` before setting body to ensure content-type
- Add `X-Request-Id` header to every response for tracing
- Structure error responses consistently: `{ success: false, error: { code, message, details? } }`

### Performance Tuning
- Oak Router uses radix tree for path matching — keep prefix depth reasonable
- Avoid `JSON.parse/stringify` in hot middleware — Oak handles this internally
- Use `ctx.send()` for large file responses instead of reading into memory
- Connection pooling for database — max 10-20 connections per Deno instance
- Compile with `deno compile --v8-flags=--max-old-space-size=512` for production

### Deployment
- Deno Deploy: entrypoint is `src/app.ts` exporting `app.handle` not `app.listen`
- Docker: user `deno:alpine` image, run as non-root user
- PM2 alternative: systemd service with `Restart=always`

## Anti-Patterns

| Anti-Pattern | Why | Fix |
|-------------|-----|-----|
| Inline routes in app.ts | Not testable, violates SRP | Separate router files |
| Mixing state and middleware | State is per-request, not app-wide | Use app.state for startup info only |
| `ctx.request.body()` multiple reads | Body stream is consumed once | Call once, store in context state |
| Missing `allowedMethods()` | Returns 404 instead of 405 on wrong method | Always add `router.allowedMethods()` |
| Hardcoded CORS origins | Security issue in production | Read from env var or config |
| No type on Application | `ctx.state` is `any` | Always parameterize `Application<AppState>` |

## Security Considerations
- Always validate `Content-Type` header — reject unexpected types
- Set `Deno.permissions` with exact `--allow-*` flags per deployment
- Use `secure: true` on cookies in production (HTTPS only)
- Rate limit by IP or token — store counters in Deno KV
- Sanitize `ctx.params` — Oak does not auto-escape/validate path params
- CORS: never `*` with credentials; use explicit origin list or regex matching

## Testing Strategies

### Unit Testing Services
```typescript
Deno.test('calculateOrderTotal', () => {
  const result = calculateTotal([{ price: 10, qty: 2 }, { price: 5, qty: 3 }])
  assertEquals(result, 35)
})
```

### Integration Testing with Database
Use in-memory KV for test isolation. Mock external HTTP calls via `std/testing/mock.ts`. Test error paths explicitly — validation errors, auth failures, 404s, 500s.

## Rules
- TypeScript strict mode — all files .ts extension.
- Router instances separate from Application — never inline routes.
- Middleware pipeline: error → logger → auth → router.
- Context state for per-request data (user, requestId).
- Oak Router prefix for route grouping — never manual path concatenation.
- Deno.env for all configuration — never hardcoded values.
- deno.json for imports — import_map.json for legacy projects.
- Always add `router.allowedMethods()` after each router.
- Never read `ctx.request.body` more than once per request.

## References
  - references/deno-runtime-guide.md — Deno Runtime Guide
  - references/oak-middleware.md — Oak Middleware
  - references/oak-performance.md — Oak Performance Optimization
  - references/oak-routing-deployment.md — Oak Routing and Deployment
  - references/oak-setup.md — Oak Setup Guide
  - references/oak-testing.md — Oak Testing
## Handoff
Hand off to `backend/universal/api-response/SKILL.md` for API response standards.

---
> Source: [j4flmao/agent-skills](https://github.com/j4flmao/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
