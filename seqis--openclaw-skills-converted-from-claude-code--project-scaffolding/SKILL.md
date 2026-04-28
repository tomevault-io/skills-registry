---
name: project-scaffolding
description: Project scaffolding and boilerplate generation for new codebases Use when this capability is needed.
metadata:
  author: seqis
---

# Project Scaffolding Skill

## Overview

Standardized project initialization workflow with templates, boilerplate generation, and structure patterns. Ensures new projects start with consistent, well-organized foundations.

## Type

workflow

## When to Invoke

**Trigger keywords:** scaffold, new project, boilerplate, template, initialize, project structure, bootstrap, starter, setup

**Trigger phrases:**
- "create a new project"
- "set up a new app"
- "initialize the project"
- "bootstrap a new..."
- "scaffold a..."

## Project Type Selection

| Type | Use When | Key Files |
|------|----------|-----------|
| **Web App** | Frontend/fullstack, browser-based | package.json, index.html, src/, public/ |
| **API/Backend** | REST/GraphQL services | app.py or main.ts, routes/, models/ |
| **CLI Tool** | Command-line applications | cli.py or index.ts, commands/ |
| **Library** | Reusable package/module | lib/, src/, package.json or pyproject.toml |
| **Microservice** | Distributed service | Dockerfile, kubernetes/, src/ |
| **Monorepo** | Multiple packages | packages/, apps/, turbo.json or nx.json |

## Essential Files Checklist

Every project MUST have:

### Root Files
- [ ] `.gitignore` - Appropriate for language/framework
- [ ] `README.md` - Project overview, setup, usage
- [ ] `LICENSE` - If open source

### Configuration
- [ ] Language config (`pyproject.toml`, `package.json`, `cargo.toml`)
- [ ] Editor config (`.editorconfig` or IDE settings)
- [ ] Linter/formatter config (eslint, prettier, ruff, black)

### Development
- [ ] `./tmp/` - For temporary/test files
- [ ] `./docs/` - Documentation folder (invoke `documentation-standards` skill)
- [ ] Test directory (`tests/`, `__tests__/`, `spec/`)

### Optional but Recommended
- [ ] `Dockerfile` - Container definition
- [ ] `docker-compose.yml` - Local development
- [ ] `.env.example` - Environment variable template
- [ ] `Makefile` or `justfile` - Common commands

## Directory Structures

### Python Project
```
project/
├── src/
│   └── project/
│       ├── __init__.py
│       ├── main.py
│       └── modules/
├── tests/
│   ├── __init__.py
│   ├── test_main.py
│   └── fixtures/
├── docs/
├── tmp/
├── pyproject.toml
├── README.md
└── .gitignore
```

### TypeScript/Node Project
```
project/
├── src/
│   ├── index.ts
│   ├── types/
│   └── modules/
├── tests/
│   └── *.test.ts
├── docs/
├── tmp/
├── package.json
├── tsconfig.json
├── README.md
└── .gitignore
```

### API Project
```
project/
├── src/
│   ├── app.ts (or main.py)
│   ├── routes/
│   ├── controllers/
│   ├── models/
│   ├── services/
│   ├── middleware/
│   └── utils/
├── tests/
├── docs/
│   ├── API_REFERENCE.md
│   └── SCHEMAS.md
├── tmp/
├── Dockerfile
├── docker-compose.yml
└── README.md
```

## Technology Selection Framework

When choosing tech stack, consider:

### Language Selection
| Factor | Python | TypeScript | Go | Rust |
|--------|--------|------------|-----|------|
| Rapid prototyping | Excellent | Good | Moderate | Slow |
| Performance | Moderate | Moderate | Excellent | Excellent |
| Type safety | Optional | Excellent | Good | Excellent |
| Ecosystem | Massive | Massive | Growing | Growing |
| Learning curve | Low | Low | Moderate | High |

### Framework Selection
| Use Case | Python | TypeScript | Go |
|----------|--------|------------|-----|
| REST API | FastAPI, Flask | Express, Fastify | Gin, Echo |
| Web App | Django, FastAPI | Next.js, Nuxt | - |
| CLI | Click, Typer | Commander, Yargs | Cobra |
| Data | Pandas, Polars | - | - |

### Database Selection
| Type | Options | Use When |
|------|---------|----------|
| Relational | PostgreSQL, SQLite | Structured data, transactions |
| Document | MongoDB | Flexible schemas, rapid iteration |
| Key-Value | Redis | Caching, sessions |
| Graph | Neo4j | Relationship-heavy data |

## Initialization Workflow

### Step 1: Define Project Type
- What problem does this solve?
- Who is the user?
- What's the deployment target?

### Step 2: Select Technology
- Language based on requirements
- Framework based on use case
- Database based on data model

### Step 3: Create Structure
- Use appropriate directory template
- Create all essential files
- Initialize version control

### Step 4: Configure Development
- Set up linting/formatting
- Configure testing framework
- Create development environment

### Step 5: Document
- Write README with setup instructions
- Invoke `documentation-standards` skill
- Create initial architecture docs

## Anti-Patterns to Avoid

| Anti-Pattern | Why Bad | Do Instead |
|--------------|---------|------------|
| No .gitignore | Commits secrets, artifacts | Always create appropriate .gitignore |
| Flat structure | Doesn't scale | Use module/feature folders |
| No tests folder | Testing becomes afterthought | Create tests/ from start |
| Hardcoded config | Environment-specific | Use .env files |
| No docs folder | Documentation neglected | Create docs/ immediately |

## Integration

Works with:
- `documentation-standards` - For project documentation
- `tdd-workflow` - For test setup
- `architecture-patterns` - For design decisions
- `/newapp` command - Invokes this skill automatically

---

*Ensures consistent, well-structured project initialization*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
