---
name: stacks
description: >- Use when this capability is needed.
metadata:
  author: mrsknetwork
---

# Stack Conventions Skill

## Purpose
Ensures generated code follows ecosystem-standard conventions rather than generic patterns. Every stack has an idiomatic way to structure projects, name files, handle dependencies, and compose modules. This skill provides the defaults so downstream skills (`generate`, `validate`) produce code that a human engineer would recognize as "correct for this stack."

## Stack Detection

Identify the active stack by scanning for file signatures in the project root:

| Signal | Stack | Action |
|---|---|---|
| `pyproject.toml`, `requirements.txt`, `Pipfile` | Python | Load `references/python-fastapi.md` |
| `package.json` + `tsconfig.json` | Node.js/TypeScript | Load `references/node-typescript.md` |
| `next.config.*`, `app/layout.tsx` | Next.js | Load `references/nextjs.md` |
| `go.mod` | Go | Load `references/go.md` |
| None detected | Unknown | Ask the user or infer from the task description |

If multiple signals are present (e.g., a monorepo with both Python and TypeScript), load all relevant references and apply each to its respective workspace.

## Stack Selection Matrix

When the user is starting a **new project** and hasn't chosen a stack:

| Project Type | Default Recommendation | Reasoning |
|---|---|---|
| AI/ML, data pipelines, inference APIs | **Python (FastAPI)** | Native ecosystem for AI inference, Pandas, data processing. FastAPI provides excellent async out of the box. |
| Full-stack web app, SaaS | **Next.js (TypeScript)** | End-to-end type safety, server components, built-in routing, Vercel deployment. |
| API-only backend, microservices | **Node.js (Express/Fastify)** or **FastAPI** | Depends on team expertise. TypeScript if frontend exists; Python if data-heavy. |
| High-throughput, low-latency services | **Go** | Superior concurrency primitives, minimal memory footprint, fast cold starts. |
| CLI tools | **Python** or **Go** | Python for quick scripts; Go for distributable binaries. |

Provide the default. Only offer alternatives if the user pushes back.

## Universal Conventions (All Stacks)

These apply regardless of stack:

1. **Environment variables** for all secrets and configuration — never hardcoded.
2. **`.env.example`** committed to the repo documenting every required env var.
3. **Dependency lockfiles** committed (`pnpm-lock.yaml`, `uv.lock`, `go.sum`).
4. **Structured logging** (JSON format) for all production services.
5. **README.md** in every project root with setup instructions, env var docs, and run commands.

## Progressive Disclosure

For detailed conventions per stack, load the appropriate reference file:

- `references/python-fastapi.md` — Project structure, naming, libraries, config patterns
- `references/node-typescript.md` — Project structure, naming, libraries, tsconfig
- `references/nextjs.md` — App Router, Server/Client Components, data fetching
- `references/go.md` — Project layout, module structure, error handling, libraries

## Handoff
After establishing the stack conventions, hand off to the `plan` skill (for new projects) or the `generate` skill (for existing codebases where the stack is already established).

---
> Source: [mrsknetwork/nemodev](https://github.com/mrsknetwork/nemodev) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-20 -->
