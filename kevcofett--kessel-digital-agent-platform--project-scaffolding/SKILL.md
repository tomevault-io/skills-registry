---
name: project-scaffolding
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Project Scaffolding

## Commands

- `/newproject` — Interactive project setup
- `/newproject <name> --stack <stack>` — Quick setup with specified stack

## Procedure

### Phase 1: Gather Requirements

Ask the user:

1. Project name
2. Stack/language (Python, TypeScript, React, Next.js, FastAPI, etc.)
3. Purpose (API, CLI, web app, library, agent, data pipeline)
4. Package manager preference (pip/uv, npm/pnpm)
5. Target directory (default: ~/Kessel-Digital/{project-name})

### Phase 2: Generate Structure

Create the project directory with appropriate boilerplate:

Python project:
- src/{package}/__init__.py, main.py
- tests/__init__.py, test_main.py
- requirements.txt or pyproject.toml
- Makefile with common targets
- .gitignore (Python template)

TypeScript/Node project:
- src/index.ts
- tests/index.test.ts
- package.json with scripts
- tsconfig.json
- .gitignore (Node template)

Common to all:
- README.md with project name, description, setup, usage sections
- .env.example with placeholder values
- .editorconfig
- LICENSE (MIT default unless specified)

### Phase 3: Initialize

1. `git init`
2. Create .gitignore appropriate for the stack
3. Stage all files
4. Create initial commit: "feat: scaffold {project-name}"

### Phase 4: Report

Show the user:
- Directory tree of what was created
- How to get started (install deps, run, test)
- Next steps suggestions

## MCMAP-Specific Templates

For agent-related projects:
- Include scripts/ directory with pac_client.py pattern
- Include .claude/ with CLAUDE.md stub
- Include memory/ directory structure
- Add Makefile with validate/import targets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
