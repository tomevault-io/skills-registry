---
name: setup-project
description: This skill should be used when the user says "setup project", "configurar proyecto", "inicializar proyecto", "agregar npm run check", "que CLIs tengo", "detect CLIs", or starts working on a new project. It configures standardized check scripts and detects available CLI tools in the environment. Use when this capability is needed.
metadata:
  author: testacode
---

# Setup Project

Skill for auto-configuring new projects with standardized scripts and detecting available CLI tools.

## Workflow

### Phase 1: Detect Project Type

Check for project indicators:

```bash
ls package.json pyproject.toml requirements.txt go.mod Cargo.toml 2>/dev/null
```

Use the detection table from `references/cli-detection.md` (section "Deteccion de Tipo de Proyecto") to determine project type and base check script.

If no project type detected:
> "No supported project files found. This skill supports Node.js, Python, Go, and Rust projects."

### Phase 2: Add Check Script

#### Node.js projects

Read `package.json` and analyze existing scripts. Build the check script based on available scripts:

| Script Exists | Include in check |
|---------------|------------------|
| `lint` | `npm run lint` |
| `typecheck` | `npm run typecheck` |
| `test` | `npm run test` |
| `build` | `npm run build` |

Example: if all exist:
```json
"check": "npm run lint && npm run typecheck && npm run test && npm run build"
```

Detect package manager using lockfile detection from `references/cli-detection.md`. Use the detected package manager in the check script (e.g., `bun run` instead of `npm run`).

**Add the check script to package.json using Edit tool.**

#### Python projects

For `pyproject.toml` projects:
```
"check": "ruff check . && pytest"
```

For `requirements.txt` only:
```
"check": "pytest"
```

Add as a script in `pyproject.toml` under `[project.scripts]` or suggest creating a `Makefile` with a `check` target.

#### Go / Rust projects

Suggest creating a `Makefile` with a `check` target using the commands from `references/cli-detection.md`.

### Phase 3: Detect Installed CLIs

Detect CLIs using the table and commands from `references/cli-detection.md`. Display results as a status table.

Only show installation commands for missing CLIs if the user asks.

### Phase 4: Detect CI/CD Workflows

```bash
ls .github/workflows/*.yml 2>/dev/null || echo "NO_WORKFLOWS"
```

**If no workflows found:**
> "No GitHub Actions workflows configured. Want me to set up basic CI for this project?"

If user agrees, suggest running the `/github-actions` skill or offer to create a basic workflow.

**If workflows exist:** report them.

### Phase 5: Update/Create Project CLAUDE.md

Check if CLAUDE.md exists. If it does, add/update an "Available CLIs" section with detected tools. If not, create a minimal one with:
- Repository overview (ask user or infer from project config)
- Available CLIs section (only installed CLIs)
- Quick start commands

### Phase 6: Output Summary

```
Setup Complete
==============

Project Type: [Node.js / Python / Go / Rust]
Package Manager: [npm / bun / pnpm]
Check Script: Added to [package.json / pyproject.toml / Makefile]
  - Includes: [lint, typecheck, test, build]

Available CLIs: X/Y
  - Installed: [list]
  - Missing: [list]

GitHub Actions: [status]

CLAUDE.md: [Updated / Created]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testacode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
