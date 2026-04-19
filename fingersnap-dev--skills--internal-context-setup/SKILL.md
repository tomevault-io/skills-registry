---
name: internal-context-setup
description: Design and scaffold the internal-context/ folder structure for repository internal knowledge. Use when user asks for project documentation, internal context organization, AI agent reference materials, or architecture documentation. Use when this capability is needed.
metadata:
  author: fingersnap-dev
---

# Internal Context Setup

## Overview
Design `internal-context/` folder structure and document scaffolds so AI agents can quickly understand repository internals.

## Workflow

### 1. Determine preferred language
Identify the user's preferred language for documentation:
- Explicit prompt instruction (e.g., "write in Korean")
- Guide files like `AGENTS.md`, `CLAUDE.md`, `.cursorrules`
- Language of the user's question

If unclear, ask explicitly: "Which language should I use for the documents?"

### 2. Check existing docs
Search for existing documentation to avoid duplication:
```
**/{internal-context/**,external-context/**,AGENT.md,AGENTS.md,CLAUDE.md,.cursorrules,.windsurfrules,.clinerules,.cursor/rules/**,.windsurf/rules/**,README.md}
```

### 3. Analyze codebase
Identify scope for documentation:
- Directory structure and module boundaries
- Build/test/deploy scripts (`package.json`, `Makefile`, `scripts/`)
- Common utilities/patterns (`utils/`, `common/`, `shared/`)
- External integrations (API clients, DB config, message queues)

### 4. Create folder and documents
```
internal-context/
├── README.md         # Purpose, table of contents, maintenance guide
├── overview.md       # Architecture, key design decisions
├── workflows.md      # Build/test/deploy procedures
├── conventions.md    # Coding conventions, common modules
└── integrations.md   # Internal/external integration points
```

Omit or merge documents based on project scale.

### 5. Document templates
Write all templates in the user's preferred language.

#### README.md
```markdown
# Internal Context

This folder contains internal knowledge for AI agent reference.

## Documents
| Document | Description |
|----------|-------------|
| [overview.md](overview.md) | Architecture, design decisions |
| [workflows.md](workflows.md) | Dev workflows, CLI commands |
| [conventions.md](conventions.md) | Coding conventions, patterns |
| [integrations.md](integrations.md) | External integrations, dependencies |

## Maintenance
- Update relevant docs when structure changes
- Never include sensitive info (API keys, passwords)
```

#### overview.md
```markdown
# Overview

## Project Purpose
<!-- TODO: Problem being solved, core value -->

## Architecture
<!-- TODO: Module/service boundaries, data flow diagram -->

### Directory Structure
<!-- TODO: Key folder roles -->

### Design Decisions
<!-- TODO: Rationale for tech choices (e.g., why this DB, framework) -->

## Reference Files
- `README.md`
- `package.json` / `pyproject.toml`
- Architecture diagrams (if any)
```

#### workflows.md
```markdown
# Workflows

## Environment Setup
<!-- TODO: Required tools, versions, env vars -->

## Build
<!-- TODO: Build commands, options -->

## Test
<!-- TODO: Test commands, coverage -->

## Deploy
<!-- TODO: Deploy procedures, environment differences -->

## Reference Files
- `package.json` scripts section
- `Makefile`, `scripts/` folder
- CI/CD config (`.github/workflows/`, `.gitlab-ci.yml`)
```

#### conventions.md
```markdown
# Conventions

## Naming Rules
<!-- TODO: File, variable, function naming conventions -->

## Code Style
<!-- TODO: Linter config, formatter, import order -->

## Common Modules
<!-- TODO: Frequently used utilities -->
| Module | Path | Purpose |
|--------|------|---------|
| ... | `src/utils/...` | ... |

## Error Handling
<!-- TODO: Error handling patterns, logging approach -->

## Reference Files
- `.eslintrc`, `.prettierrc`, `ruff.toml`
- `src/utils/`, `src/common/`
```

#### integrations.md
```markdown
# Integrations

## External Services
<!-- TODO: APIs, auth methods (exclude actual keys) -->
| Service | Purpose | Config Location |
|---------|---------|-----------------|
| ... | ... | `src/config/...` |

## Database
<!-- TODO: DB type, ORM, migration method -->

## Message Queue / Events
<!-- TODO: Queue system, topic structure -->

## Reference Files
- DB config files
- API client modules
```

## Output
- Create `internal-context/` folder with scaffold documents
- Include `<!-- TODO: ... -->` placeholders in each document
- Summarize information gaps and items to fill

## Heuristics
- Omit sections if project lacks relevant components
- Preserve existing content if `internal-context/` already exists
- Never include sensitive info (API keys, passwords, PII)
- Keep document hierarchy minimal, avoid unnecessary nesting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fingersnap-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
