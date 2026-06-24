---
name: api-mock
description: Generate a working mock server from an OpenAPI/Swagger spec or from a verbal API description. Outputs Express (Node), FastAPI (Python), or Hono (TypeScript). Use when this capability is needed.
metadata:
  author: kasimmj
---

# API Mock Skill

You are generating a **mock HTTP server**. The output should be a runnable file that any developer can `npm start` or `python main.py` to test against.

## Step 1 — Identify the source

Look for, in order:
1. An OpenAPI/Swagger file (`openapi.yaml`, `swagger.json`)
2. A REST description in the user's message
3. A `routes.md` / `API.md` in the repo

If unclear, **ask** for the API shape.

## Step 2 — Pick the framework

Detect from existing project files:
- `package.json` with Express/Fastify/Hono → match it
- `requirements.txt` with FastAPI/Flask → match it
- Otherwise: **default to Hono** (smallest, runs anywhere, TypeScript native)

## Step 3 — Generate the mock

For each endpoint:
- Return realistic mock data using `faker` (Node) or `Faker` (Python)
- Honor status codes — if the spec says 201, return 201
- Add `Content-Type` headers
- Add a 200–800ms artificial latency (configurable)
- Support pagination: `?limit=N&page=N`

For errors:
- `/error/500` — always returns 500
- `/error/timeout` — never responds
- `/error/slow` — 10s delay
- These help frontend devs test error states.

## Step 4 — Add CORS

Always enable CORS with `Access-Control-Allow-Origin: *` for the mock. It's a dev tool — security doesn't apply.

## Step 5 — Output

A single file with:
- All routes
- Faker setup with a fixed seed (reproducible data)
- Healthcheck at `GET /health`
- An `if __name__` or `app.listen()` block
- A header comment with the run command

## When NOT to use

- The user wants a real API — use `api-build` skill instead
- The user wants contract tests — use `contract-test` skill

## Failure modes

- ⚠️ Don't add auth to the mock unless asked. Defeats the purpose.
- ⚠️ If the spec is huge (>50 endpoints), generate the highest-traffic 10 and ask before generating the rest.

---
> Source: [kasimmj/claude-skills-mega](https://github.com/kasimmj/claude-skills-mega) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
