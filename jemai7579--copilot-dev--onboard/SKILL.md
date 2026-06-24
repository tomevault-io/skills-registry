---
name: onboard
description: Project setup and configuration generation. Scans existing code, asks questions, generates copilot-instructions.md, coding standards, docs structure, and bootstrap scripts. Use when this capability is needed.
metadata:
  author: jemai7579
---

# Onboard — Project Setup

Set up the Agent Smith workflow on any project — greenfield or brownfield.

## What It Does

1. **Scan the project** — detect tech stack, frameworks, existing docs, build system
2. **Ask questions** — project name, goals, cloud platform, auth strategy
3. **Generate configuration** — customized for this specific project
4. **Scaffold docs structure** — create the standard documentation tree

## Generated Files

| File | Purpose |
|------|---------|
| `.github/copilot-instructions.md` | Master project config (customized) |
| `.github/instructions/coding-standards.instructions.md` | Tech-stack-specific conventions |
| `docs/prd.md` | PRD template with project name |
| `docs/product-design.md` | Design doc template |
| `docs/technical-design.md` | Technical doc template |
| `docs/api-reference.md` | API reference template |
| `docs/database-schema.md` | Schema doc template |
| `docs/code-architecture.md` | Code architecture template |
| `docs/developer-guide.md` | Dev setup guide |
| `docs/user-guide.md` | End-user guide |
| `docs/deployment-guide.md` | Deployment guide |
| `docs/backlog/README.md` | Status dashboard |
| `docs/backlog/epics/` | Epic files directory |
| `docs/backlog/done/` | Completed epics directory |
| `scripts/session-bootstrap.*` | Session init script |

## Onboarding Flow

### Step 1: Detect Project State

Scan for:
- `package.json`, `*.csproj`, `go.mod`, `requirements.txt`, `Cargo.toml` → language/framework
- `Dockerfile`, `docker-compose.yml` → containerization
- `.github/workflows/` → existing CI/CD
- `docs/` → existing documentation
- `README.md` → project description
- Test files → testing framework

Report findings:
```
📋 Project Scan Results:
- Language: TypeScript + Python
- Frontend: React (Vite)
- Backend: FastAPI
- Database: PostgreSQL (SQLAlchemy)
- Tests: Pytest + Vitest
- Docs: README.md only (no structured docs)
- CI: GitHub Actions (build + test)
```

### Step 2: Ask Configuration Questions

1. **Project name**: used in doc titles
2. **Brief description**: one-liner for context
3. **Is this greenfield or brownfield?**
   - Greenfield → "Start with Product Writer to define your PRD"
   - Brownfield → "Start with Code Architect to document what exists"
4. **Cloud platform** (if any): Azure / AWS / GCP / self-hosted / undecided
5. **Auth strategy** (if any): OAuth / JWT / API keys / undecided
6. **Team size**: solo / small team / large team (affects workflow complexity)

### Step 3: Generate Configuration

Based on scan + answers, generate:

**`.github/copilot-instructions.md`** containing:
- Project overview (from answers)
- Working style (step-by-step, decision-driven, TDD)
- Build & test commands (from scan)
- Documentation strategy (standard doc structure)
- Pre-commit verification steps (from scan)
- Agent/skill reference table

**`.github/instructions/coding-standards.instructions.md`** with:
- Tech-stack-specific patterns (detected from scan)
- TDD workflow
- Git conventions
- Security rules

**`scripts/session-bootstrap.*`** (PowerShell + bash) that:
- Shows recent git history
- Scans backlog for task statuses
- Lists ready tasks
- Reports project health

### Step 4: Scaffold Docs

Create the full `docs/` tree with templates:
- Each doc file has section headings from the standard structure
- `<!-- TODO -->` placeholders for content not yet written
- Project name and description pre-filled

### Step 5: Verify Build/Test Commands

Try running detected build/test commands to confirm they work:
- `npm run build`, `npm test`, `dotnet build`, `go test`, etc.
- Record verified commands in `copilot-instructions.md`

### Step 6: Report & Recommend Next Steps

```
✅ Onboarding Complete!

Generated:
- .github/copilot-instructions.md
- .github/instructions/coding-standards.instructions.md
- docs/ (12 template files)
- docs/backlog/ (dashboard + directories)
- scripts/session-bootstrap.ps1

Build commands verified:
- ✅ npm run build
- ✅ npm test
- ❌ npm run lint (not configured)

Recommended next step:
- Greenfield: Activate Product Writer agent → define your PRD
- Brownfield: Activate Code Architect agent → document existing patterns
```

---
> Source: [jemai7579/copilot-dev](https://github.com/jemai7579/copilot-dev) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
