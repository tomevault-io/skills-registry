---
name: project-documentation
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# Project Documentation

Analyze and document existing codebases to enable AI-assisted development. Systematically scan projects, detect architecture patterns, and generate structured documentation that helps both humans and AI agents understand how to extend the codebase.

## Scanning Approach

Perform a thorough scan because AI agents downstream need complete context to make safe code changes. This means:

- **Prioritize source directories** -- `src/`, `app/`, `lib/`, `components/`, `api/`, `services/`
- **Skip generated and vendor code** -- `node_modules/`, `dist/`, `build/`, `.next/`, `vendor/`, `__pycache__/`. These are reproducible and add noise without insight
- **Sample large test suites** -- read representative test files to understand patterns, then note coverage scope. Reading every test file in a 500-file suite is diminishing returns
- **Read all config files** -- `package.json`, `tsconfig.json`, `.env.example`, CI configs. These are small and high-signal
- **Trace entry points fully** -- follow the main entry points through the call graph to understand the core architecture

The goal is to produce documentation thorough enough that an AI agent could plan and implement a new feature using only these docs as context.

## Workflow

### Step 1: Detect Project Structure

Analyze the project root:

- Identify package managers (`package.json`, `go.mod`, `requirements.txt`, `Cargo.toml`)
- Detect framework markers (`next.config.*`, `nuxt.config.*`, `angular.json`)
- Analyze directory layout (`src/`, `app/`, `lib/`, `components/`)
- Determine repo type: monolith, monorepo, or multi-part

See [references/project-types.md](./references/project-types.md) for classification details.

### Step 2: Classify Project Type

Match against known types: **web**, **mobile**, **backend**, **cli**, **library**, **desktop**, **data**, **infra**. See [references/project-types.md](./references/project-types.md) for key patterns and critical directories per type.

### Step 3: Scan the Codebase

Run these scans against all relevant source code:

| Scan | Focus Areas | Key Directories |
|------|-------------|-----------------|
| API | Routes, HTTP methods, auth, middleware | `controllers/`, `routes/`, `api/`, `handlers/` |
| Data Models | Schemas, relationships, constraints, migrations | `models/`, `schemas/`, `prisma/`, `migrations/` |
| UI Components | Component library, props, design patterns | `components/`, `ui/`, `widgets/` |
| State Management | Stores, actions, data flow, side effects | `store/`, `state/`, `context/` |
| Configuration | Env vars, build config, feature flags | root config files, `.env.example` |
| Tests | Frameworks, patterns, fixtures, coverage | `tests/`, `__tests__/`, `spec/` |

### Step 4: Generate Documentation

Generate documentation files into `docs/` (or user-specified location). By default, produce all 12 documents. Each template is in `references/`.

| Document | Template | Purpose |
|----------|----------|---------|
| `index.md` | [references/index.md](./references/index.md) | Master navigation hub |
| `project-overview.md` | [references/project-overview.md](./references/project-overview.md) | Executive summary |
| `source-tree-analysis.md` | [references/source-tree-analysis.md](./references/source-tree-analysis.md) | Annotated directory tree |
| `architecture.md` | [references/architecture.md](./references/architecture.md) | Technical architecture |
| `component-inventory.md` | [references/component-inventory.md](./references/component-inventory.md) | UI component catalog |
| `development-guide.md` | [references/development-guide.md](./references/development-guide.md) | Setup and workflow |
| `api-contracts.md` | [references/api-contracts.md](./references/api-contracts.md) | API documentation |
| `data-models.md` | [references/data-models.md](./references/data-models.md) | Database schemas |
| `state-management.md` | [references/state-management.md](./references/state-management.md) | State patterns and flow |
| `testing-guide.md` | [references/testing-guide.md](./references/testing-guide.md) | Test structure |
| `configuration.md` | [references/configuration.md](./references/configuration.md) | Environment config |
| `deployment-guide.md` | [references/deployment-guide.md](./references/deployment-guide.md) | Deployment process |

Optional: [references/adr.md](./references/adr.md) for Architecture Decision Records.

When generating each document:

1. Read the template from `references/`
2. Fill all `{placeholder}` values with actual findings from the scan
3. Mark truly inapplicable sections as "Not applicable to this project type"

### Step 5: Validate

Use [references/validation-checklist.md](./references/validation-checklist.md) to verify:

- No `{placeholder}` text remains
- No "TODO" or "TBD" markers
- All links resolve correctly
- Examples are realistic
- Mermaid diagrams render

## Presets

Users can request a focused subset instead of all 12 documents:

| Preset | Documents |
|--------|-----------|
| `--preset=core` | index, project-overview, source-tree-analysis, architecture, development-guide |
| `--preset=api` | index, architecture, api-contracts, data-models, configuration |
| `--preset=frontend` | index, architecture, component-inventory, state-management, testing-guide |
| `--preset=devops` | index, configuration, deployment-guide, testing-guide |
| `--preset=minimal` | index, project-overview, architecture |

Users can also select or exclude individual documents:
- *"Generate only: architecture, api-contracts, data-models"*
- *"Generate full docs except: component-inventory, state-management"*

`index.md` is included with every selection because it serves as the navigation hub.

## Multi-Part Projects

For projects with multiple parts (client/server, microservices):

1. Detect all parts by scanning for separate package managers
2. Document each part with part-specific files (e.g., `architecture-client.md`)
3. Create an integration document covering communication patterns and shared dependencies
4. Link everything from a unified `index.md`

## Write-As-You-Go

To manage context efficiently during generation:

1. Write each document immediately after generating it (do not hold all docs in memory)
2. Validate each section before moving on
3. Keep only summaries in working context after writing

## Example Output

A generated `project-overview.md` looks like this (abbreviated):

```markdown
# MyApp - Project Overview

**Type:** Web Application (React + TypeScript)
**Framework:** Next.js 14 (App Router)
**Database:** PostgreSQL via Prisma ORM
**State:** Zustand + React Query

## Architecture Summary

Server-rendered React application using Next.js App Router with
API routes serving a PostgreSQL database through Prisma. Authentication
via NextAuth.js with Google and GitHub providers.

## Key Entry Points

| Entry Point | Path | Purpose |
|-------------|------|---------|
| App root | `src/app/layout.tsx` | Root layout with providers |
| API routes | `src/app/api/` | REST endpoints |
| DB schema | `prisma/schema.prisma` | Data model definitions |

## Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Frontend | React | 18.2 |
| Framework | Next.js | 14.1 |
| Database | PostgreSQL | 15 |
| ORM | Prisma | 5.8 |
| Auth | NextAuth.js | 4.24 |
```

## Best Practices

- **CommonMark compliance** -- all markdown must follow CommonMark spec
- **No time estimates** -- they vary too much to be useful
- **Active voice, present tense** -- "The function returns" not "The function will return"
- **Task-oriented** -- write for user goals, not feature lists
- **Mermaid diagrams** -- use appropriate types (flowchart, sequenceDiagram, erDiagram, classDiagram). Keep diagrams focused: 5-10 nodes ideal, 15 max

## Additional References

- [Project Type Detection](./references/project-types.md)
- [Example Prompts](./references/examples.md)
- [Validation Checklist](./references/validation-checklist.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
