---
name: bun-elysia-expert
description: Build production-ready optimized APIs with Bun.js, Elysia.js, and Prisma ORM. Use this skill for API development, PostgreSQL database integration, optimized queries, deployment to Railway/VPS (with or without Docker), Nginx configuration, and SSL setup. Covers routing, validation, authentication, WebSocket, Swagger/Scalar documentation, caching with Redis, and end-to-end type safety with Eden. Use when this capability is needed.
metadata:
  author: muke-coder
---

# Bun + Elysia.js Full-Stack Development

Build high-performance, production-ready full-stack applications with Bun runtime, Elysia.js framework, and Prisma ORM.

## Quick Start

### New Elysia Project

```bash
bun create elysia my-app
cd my-app
bun run dev
```

### Basic Server

```typescript
import { Elysia, t } from "elysia";

const app = new Elysia()
  .get("/", () => "Hello, World!")
  .get("/users/:id", ({ params }) => ({ id: params.id }), {
    params: t.Object({ id: t.Number() }),
  })
  .post("/users", ({ body }) => ({ created: body }), {
    body: t.Object({
      name: t.String(),
      email: t.String({ format: "email" }),
    }),
  })
  .listen(3000);
```

### With Bun Native APIs

```typescript
import { Elysia } from "elysia";
import { sql } from "bun";
import { redis } from "bun";

const app = new Elysia()
  .decorate("sql", sql)
  .decorate("redis", redis)
  .get("/users", async ({ sql }) => {
    return sql`SELECT * FROM users`;
  })
  .get("/cache/:key", async ({ redis, params }) => {
    return redis.get(params.key);
  })
  .listen(3000);
```

---

## Reference Navigation

### What do you want to do?

**Set up a project:**

- Monorepo with workspaces → [monorepo.md](references/monorepo.md)
- Deploy to production → [deployment.md](references/deployment.md)

**Use Bun native APIs:**

- PostgreSQL/MySQL/SQLite → [native-apis/sql.md](references/native-apis/sql.md)
- Redis/Valkey → [native-apis/redis.md](references/native-apis/redis.md)
- S3/Object storage → [native-apis/s3.md](references/native-apis/s3.md)
- HTTP server basics → [native-apis/http-server.md](references/native-apis/http-server.md)

**Database with Prisma ORM:**

- Setup, queries, optimizations → [prisma-guide.md](references/prisma-guide.md)

**Build with Elysia:**

- Routing and handlers → [elysia/core.md](references/elysia/core.md)
- Request/response validation → [elysia/validation.md](references/elysia/validation.md)
- Hooks, middleware, plugins → [elysia/lifecycle.md](references/elysia/lifecycle.md)
- WebSocket support → [elysia/websocket.md](references/elysia/websocket.md)
- OpenAPI/Swagger/Scalar docs → [elysia/swagger.md](references/elysia/swagger.md)
- Type-safe client (Eden) → [elysia/eden.md](references/elysia/eden.md)

**API Design Best Practices:**

- Performance optimization patterns → [api-guide.md](references/api-guide.md)

**Deploy to Production:**

- Railway (with/without Docker) → [deployment.md](references/deployment.md#1-railway-deployment)
- VPS with Docker → [deployment.md](references/deployment.md#21-vps-with-docker)
- VPS manual setup → [deployment.md](references/deployment.md#22-vps-without-docker)
- Nginx reverse proxy → [deployment.md](references/deployment.md#3-nginx-configuration)
- SSL/TLS with Let's Encrypt → [deployment.md](references/deployment.md#4-ssltls-with-lets-encrypt)

**Migrate from Fastify:**

- Routing, params, body → [migration/fastify-basics.md](references/migration/fastify-basics.md)
- Schema validation → [migration/fastify-validation.md](references/migration/fastify-validation.md)
- Lifecycle hooks → [migration/fastify-hooks.md](references/migration/fastify-hooks.md)
- Plugin architecture → [migration/fastify-plugins.md](references/migration/fastify-plugins.md)
- Authentication → [migration/fastify-auth.md](references/migration/fastify-auth.md)
- WebSocket, Swagger, files → [migration/fastify-advanced.md](references/migration/fastify-advanced.md)

---

## Common Patterns

### Authentication Middleware

```typescript
import { Elysia } from "elysia";
import { jwt } from "@elysiajs/jwt";

const app = new Elysia()
  .use(jwt({ secret: process.env.JWT_SECRET! }))
  .derive(async ({ headers, jwt }) => {
    const token = headers.authorization?.replace("Bearer ", "");
    const user = token ? await jwt.verify(token) : null;
    return { user };
  })
  .get("/profile", ({ user, error }) => {
    if (!user) return error(401);
    return user;
  });
```

### Database with Validation

```typescript
import { Elysia, t } from "elysia";
import { sql } from "bun";

const app = new Elysia().post(
  "/users",
  async ({ body }) => {
    const [user] = await sql`
      INSERT INTO users (name, email)
      VALUES (${body.name}, ${body.email})
      RETURNING *
    `;
    return user;
  },
  {
    body: t.Object({
      name: t.String({ minLength: 1 }),
      email: t.String({ format: "email" }),
    }),
  },
);
```

### Grouped Routes with Guard

```typescript
app.group("/api", (app) =>
  app.guard(
    {
      headers: t.Object({
        authorization: t.String(),
      }),
      beforeHandle: ({ headers, error }) => {
        if (!isValidToken(headers.authorization)) {
          return error(401);
        }
      },
    },
    (app) => app.get("/users", getUsers).post("/users", createUser),
  ),
);
```

### WebSocket Chat

```typescript
app.ws("/chat", {
  body: t.Object({
    room: t.String(),
    message: t.String(),
  }),
  open(ws) {
    ws.subscribe("general");
  },
  message(ws, { room, message }) {
    ws.publish(room, { from: ws.id, message });
  },
});
```

---

## Key Differences: Fastify vs Elysia

| Fastify                    | Elysia                     |
| -------------------------- | -------------------------- |
| `request.params`           | `{ params }`               |
| `request.query`            | `{ query }`                |
| `request.body`             | `{ body }`                 |
| `reply.code(n).send(x)`    | `set.status = n; return x` |
| `reply.header(k, v)`       | `set.headers[k] = v`       |
| JSON Schema                | TypeBox (`t.*`)            |
| `preHandler` hook          | `beforeHandle`             |
| `fastify.decorate()`       | `.decorate()`              |
| `fastify.register(plugin)` | `.use(plugin)`             |

---

## Installation

```bash
# Elysia and plugins
bun add elysia @elysiajs/swagger @elysiajs/jwt @elysiajs/cors @elysiajs/static @elysiajs/eden

# Prisma ORM
bun add prisma @prisma/client
bunx prisma init --datasource-provider postgresql

# Types
bun add -d @types/bun typescript
```

---

## Prisma Integration

### Basic Setup with Elysia

```typescript
import { Elysia, t } from "elysia";
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

const app = new Elysia()
  .decorate("prisma", prisma)
  .get("/users", async ({ prisma }) => {
    return prisma.user.findMany({
      select: { id: true, email: true, name: true },
    });
  })
  .post(
    "/users",
    async ({ prisma, body }) => {
      const user = await prisma.user.create({
        data: { email: body.email, name: body.name },
      });
      return { id: user.id };
    },
    {
      body: t.Object({
        email: t.String({ format: "email" }),
        name: t.String(),
      }),
    },
  )
  .onStop(async () => {
    await prisma.$disconnect();
  })
  .listen(3000);
```

---

## Swagger/Scalar Documentation

Scalar is the default documentation UI for Elysia's swagger plugin.

```typescript
import { Elysia, t } from "elysia";
import { swagger } from "@elysiajs/swagger";

const app = new Elysia()
  .use(
    swagger({
      documentation: {
        info: {
          title: "My API",
          version: "1.0.0",
        },
        tags: [{ name: "users", description: "User operations" }],
      },
      path: "/docs", // Scalar UI at /docs
      // provider: "swagger-ui", // Use this to switch to Swagger UI
      scalarConfig: {
        theme: "purple",
      },
    }),
  )
  .get("/users", () => "List users", {
    detail: {
      summary: "Get all users",
      tags: ["users"],
    },
  })
  .listen(3000);

// Docs: http://localhost:3000/docs
// JSON: http://localhost:3000/docs/json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muke-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
