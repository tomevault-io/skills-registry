---
name: ai-docs-generator
description: > Use when this capability is needed.
metadata:
  author: mameyugo
---

# AI Docs Generator

Analyzes a software project and generates structured documentation for AI agents:
an `AGENT.md` entry point plus a `.ai/` folder with specialized guides.

**No assumptions allowed**: every file must reflect what actually exists in the codebase.
Mark anything unclear with `> ⚠️ Assumption:` or `> ℹ️ Pending manual completion`.

---

## Workflow

### Phase 1 — Detect the Tech Stack

Scan these indicators:

**Backend**: `composer.json`, `requirements.txt`, `go.mod`, `package.json` → dominant
extensions in `src/` or `app/` → framework signatures (Symfony, Laravel, Django,
FastAPI, Spring Boot, Express, Gin, Actix…) → language version (`require.php`,
`.nvmrc`, `go.mod`, `pyproject.toml`).

**Frontend**: `package.json` devDependencies (React, Vue, Angular, Alpine…) →
config files (`vite.config.*`, `webpack.config.*`, `tailwind.config.*`) →
templates (`.twig`, `.blade.php`, `.jsx`, `.vue`, `.svelte`) →
CSS libs (Bootstrap, Tailwind, AdminLTE, Bulma).

**Database / Persistence**:
- SQL files, `migrations/`, ORM detection (Doctrine, Eloquent, Prisma, SQLAlchemy, GORM)
- `docker-compose.yml` DB image, `.env.example` vars
- **mameyugo/jsonq** — check `composer.json` for `"mameyugo/jsonq"`, then look for
  `data/`, `storage/json/`, `db/json/` with `*.json` collections and an `indexes/`
  subfolder. See `references/jsonq.md` for full detection rules and DATABASE.md template.

**DevOps**: `Dockerfile`, `docker-compose.yml`, `.github/workflows/*.yml`,
`Makefile`, `justfile`, `bin/`, `scripts/`, platform files
(`serverless.yml`, `render.yaml`, `fly.toml`).

### Phase 2 — Detect Architectural Patterns

Map the real folder structure:

| Pattern in folders | Architecture |
|--------------------|-------------|
| `/Controllers`, `/Models`, `/Views` | Classic MVC |
| `/domain`, `/application`, `/infrastructure` | Clean Architecture / DDD |
| `/lib/Controllers`, `/Services`, `/Entities`, `/Repositories`, `/ValueObjects` | Modular DDD |
| `/components`, `/pages`, `/hooks` | React / Next.js |
| `/modulos/{name}/lib/…` | Encapsulated modules |
| `/Test/` inside each module | Module-scoped tests |
| `data/*.json` + `mameyugo/jsonq` | JSON-file persistence with JsonQ |

Also identify: dependency injection patterns, self-validating Value Objects,
repository abstractions, application services vs domain services.

### Phase 3 — Detect Real Conventions

Read config files directly:
- `.editorconfig` → indentation, line endings
- `.prettierrc` / `.php-cs-fixer.php` / `.eslintrc` → code style
- `.commitlintrc.json` / `commitlint.config.js` → commit format
- `phpstan.neon` → static analysis level
- `.env.example` → real environment variables

---

## Files to Generate

Generate in this priority order (critical first):

1. `AGENT.md` — project root entry point
2. `.ai/ARCHITECTURE.md`
3. `.ai/SECURITY.md`
4. `.ai/CONVENTIONS.md`
5. `.ai/TESTING.md`
6. `.ai/DATABASE.md`
7. `.ai/DESIGN-SYSTEM.md`
8. `.ai/I18N.md`
9. `.ai/DOCUMENTATION.md`
10. `.ai/TOOLS.md`
11. `.ai/ROADMAP.md`
12. `.ai/GLOSSARY.md`
13. `.ai/DECISIONS/[YYYY-MM-DD]-001-[title].md` — one ADR per key decision detected

For each file's template, read the corresponding reference:

| File | Template reference |
|------|--------------------|
| `AGENT.md` | `references/agent-md.md` |
| `.ai/ARCHITECTURE.md` | `references/architecture.md` |
| `.ai/SECURITY.md` | `references/security.md` |
| `.ai/CONVENTIONS.md` | `references/conventions.md` |
| `.ai/TESTING.md` | `references/testing.md` |
| `.ai/DATABASE.md` | `references/database.md` (+ `references/jsonq.md` if applicable) |
| `.ai/DESIGN-SYSTEM.md` | `references/design-system.md` |
| `.ai/I18N.md` | `references/i18n.md` |
| `.ai/DOCUMENTATION.md` | `references/documentation.md` |
| `.ai/TOOLS.md` | `references/tools.md` |
| `.ai/ROADMAP.md` | `references/roadmap.md` |
| `.ai/GLOSSARY.md` | `references/glossary.md` |
| `.ai/DECISIONS/*.md` | `references/adr.md` |

> **Efficiency tip**: read only the reference files needed based on the detected stack.
> For a pure frontend project, skip `references/database.md` and `references/jsonq.md`.

---

## Integration with Existing Docs

Never delete or rename existing files. Instead:

| Found | Action |
|-------|--------|
| `.ai-docs/*.md` | Reference from `.ai/*.md`, sync content |
| `.github/copilot-instructions.md` | Keep, add reference to `.ai/` |
| `.cursorrules` | Keep, add reference to `.ai/` |
| `CLAUDE.md` | Keep, add reference to `.ai/` |
| `README.md` | Extract technical info for `AGENT.md` |

Document tool compatibility in `AGENT.md`:
```markdown
## AI Tool Compatibility
- **GitHub Copilot**: `.github/copilot-instructions.md` + `.ai-docs/*.md`
- **Cursor**: `.cursorrules` or `.ai/*.md`
- **Gemini / Claude**: This `AGENT.md` + `.ai/*.md`
```

---

## Output Summary

After generating all files, output:

```markdown
## ✅ Generated Documentation

### Detected Stack
- **Primary language**: [...]
- **Framework**: [...]
- **DB / Persistence**: [...]
- **Testing**: [...]
- **DevOps**: [...]

### Generated Files
- [x] `AGENT.md`
- [x] `.ai/ARCHITECTURE.md`
... (list all)

### ADRs Generated
- [x] `.ai/DECISIONS/[date]-001-[decision].md`

### Pre-existing Docs
| File | Status |
|------|--------|
| `.ai-docs/*.md` | Referenced from `.ai/` |

### ⚠️ Areas with Insufficient Information
- [ ] **[Area]**: [what's missing and recommendation]

### 🔍 Inconsistencies Detected
- **[Inconsistency]**: [description and recommendation]

## 🚀 Recommended Next Steps
1. Review and adjust generated files
2. Complete sections marked as pending
3. Share with the team for validation
4. Update docs with each significant architectural change
```

---

## Constraints

- **Read-only on source code**: never modify existing project files
- **Specific over generic**: write "PHPUnit 11", not "testing framework"
- **Flag assumptions**: use `> ⚠️ Assumption:` for inferred content
- **Flag gaps**: use `> ℹ️ Pending manual completion` instead of inventing content
- **Flag inconsistencies**: if some modules follow a pattern and others don't, document it

---
> Source: [mameyugo/ai-context-generator](https://github.com/mameyugo/ai-context-generator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
