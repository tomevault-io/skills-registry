---
name: koa-advanced-patterns
description: Use when working with a comprehensive guide for developing robust, scalable, and secure Node.js applications using the Koa framework.
metadata:
  author: ecelayes
---

# Koa.js Professional Standards

## Middleware Architecture
1.  **The Async Promise Chain:** ALWAYS declare middleware as `async (ctx, next) => { ... }`.
2.  **Await Next:** You MUST `await next()` exactly once in every middleware.
3.  **Execution Order:** Code before `await next()` handles the Request; code after handles the Response.

## Reliability & Ops
1.  **Graceful Shutdown:** Do not kill the server instantly. Listen for system signals (`SIGTERM`, `SIGINT`).
    - Pattern: Stop accepting new requests -> Close database connections -> Exit process.
    - Example: `server.close(() => db.disconnect())`.
2.  **Health Checks:** Always implement a `/health` endpoint for load balancers (return 200 OK if DB is connected).

## Context (ctx) Mastery
1.  **State Management:** Use `ctx.state` to pass data between middleware (e.g., `ctx.state.user`). NEVER pollute the global namespace.
2.  **Request Data:** Access via `ctx.request.body`, `ctx.query`, or `ctx.params`.
3.  **Response Construction:** Explicitly set `ctx.status` before setting `ctx.body`.

## Security Best Practices
1.  **Error Handling:** Implement a top-level `app.on('error', ...)` listener and a generic try/catch middleware. Never crash the process on a request error.
2.  **Headers:** Use `koa-helmet` for standard security headers.
3.  **Cookies:** Always sign cookies using `ctx.cookies.set(name, val, { signed: true })` and a secure `app.keys`.

## Routing
1.  **Router Organization:** Use `koa-router` (or `@koa/router`).
2.  **Prefixing:** Group routes by domain (e.g., `const usersRouter = new Router({ prefix: '/users' })`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ecelayes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
