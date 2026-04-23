---
name: codebase-mapping
description: Generate structured codebase maps with dependency graphs, file Use when this capability is needed.
metadata:
  author: vneseyoungster
---

# Codebase Mapping Skill

## Purpose
Produce consistent, structured documentation of codebase organization.

## When to Use
- Starting work on unfamiliar project
- Onboarding new team members
- Documenting architecture decisions
- Before major refactoring

## Output Template
Use the template in [templates/structure-report.md](templates/structure-report.md)

## Mapping Process

### Step 1: Project Identification
Identify project type from configuration files:
- `package.json` → Node.js
- `pyproject.toml` / `setup.py` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust
- `pom.xml` / `build.gradle` → Java

### Step 2: Structure Analysis
Map directories to their purposes:
- `src/` or `lib/` → Source code
- `tests/` or `__tests__/` → Test files
- `docs/` → Documentation
- `scripts/` → Build/utility scripts
- `config/` → Configuration files

### Step 3: Dependency Graph
Create simplified dependency visualization:
```
Entry Point
├── Core Module A
│   ├── Utility 1
│   └── Utility 2
├── Core Module B
│   └── External Lib
└── Shared Components
```

### Step 4: Key Files
Identify and document:
- Entry points (main.ts, index.js, app.py)
- Configuration (tsconfig, eslint, etc.)
- Environment handling
- Build configuration

## Storage Location
Save output to: `docs/research/codebase-map-{date}.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vneseyoungster) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
