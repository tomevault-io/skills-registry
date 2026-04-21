---
name: fastify-best-practices
description: Guidelines, best practices, and common pitfalls to avoid when working with Node.js and Fastify. Use when developing APIs, reviewing Fastify code, or refactoring. Use when this capability is needed.
metadata:
  author: nam088
---

# Fastify Best Practices & Pitfalls

## When to use
- When creating new Fastify routes or plugins.
- When debugging performance issues or common bugs in Fastify.
- When reviewing code for security and stability.

## Things to AVOID (Pitfalls)

### 1. Blocking the Event Loop
- **Avoid**: Synchronous heavy computation (CPU-bound tasks) inside route handlers.
- **Why**: Node.js is single-threaded. Blocking logic stops the server from handling other requests.
- **Fix**: Offload to worker threads or optimize algorithms.

### 2. `console.log` for Logging
- **Avoid**: Using `console.log`, `console.error` in production.
- **Why**: It is synchronous and slow; it blocks the event loop.
- **Fix**: Use Fastify's built-in logger (`request.log` or `fastify.log`) which uses **Pino** (async, fast).

### 3. Missing Schemas
- **Avoid**: Defining routes without JSON schemas for request/response.
- **Why**: Schemas provide faster serialization (Fastify optimizes JSON parsing) and automatic validation/documentation (Swagger).
- **Fix**: Always define `schema` in route options.

### 4. Global State Mutation
- **Avoid**: Modifying global variables or `fastify` instance properties directly inside request handlers.
- **Why**: Causes race conditions and cross-request pollution.
- **Fix**: Use `decorateRequest` or request-scoped context.

### 5. Incorrect Plugin Encapsulation
- **Avoid**: Registering plugins that accidentally affect parent or sibling contexts.
- **Fix**: Use `fastify-plugin` (`fp`) to break encapsulation when you *want* to share decorators, but understand Fastify's encapsulation model (DAG) to keep plugins isolated when needed.

### 6. Using Arrow Functions for Handlers (Context Binding)
- **Warning**: Be careful when using arrow functions if you rely on `this` context in Fastify (though standard usage often relies on `request`/`reply` args).
- **Best Practice**: Prefer explicit arguments `(request, reply) => ...` over relying on `this`.

## Best Practices

### 1. Dependency Injection
- Register services, db connections, and configs as plugins or decorators early in the boot sequence.

### 2. Error Handling
- Use `fastify.setErrorHandler` to centralize error formatting.
- Do not let promises hang; always `await` or return the promise.

### 3. Graceful Shutdown
- Handle `SIGINT` / `SIGTERM`.
- Close database connections and `fastify.close()`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nam088) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
