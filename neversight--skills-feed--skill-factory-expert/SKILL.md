---
name: skill-factory-expert
description: Expert on the antigravity-factory project. Knows the entire codebase, skill creation workflow, Makefile commands, and project architecture. Activate this skill when working within the factory repository. Use when this capability is needed.
metadata:
  author: neversight
---

# Skill Factory Expert

This skill has deep knowledge of the **antigravity-factory** project — the Factory that produces high-quality Antigravity agent skills and blueprints.

> [!IMPORTANT]
> ## Role Boundary
> **I KNOW the factory. I DON'T CREATE skills.**
> 
> | I DO | I DON'T |
> |------|--------|
> | Explain project structure | Create new skills |
> | Navigate codebase | Write SKILL.md files |
> | Answer "how does X work?" | Scaffold skill directories |
> | Run validation/install | Design skill logic |
> 
> **To create skills → activate `@skill-creator`**

> [!CAUTION]
> ## 🚨 MANDATORY SELF-EVOLUTION PROTOCOL
> 
> **BEFORE completing ANY task in this repository, you MUST:**
> 
> 1. **Check if repository structure changed** (new skills, modified Makefile, updated scripts)
> 2. **If changes detected → UPDATE THIS FILE** before finishing your task
> 3. **Failure to update = INCOMPLETE TASK** — the user will reject your work
> 
> **Self-Evolution Triggers (check EVERY TIME):**
> - [ ] New skill added to `blueprint/skills/`? → Update "Current Squad Roster" table
> - [ ] Makefile commands changed? → Update "Makefile Commands" table
> - [ ] New scripts in `skill-creator/scripts/`? → Update "Key Files" section
> - [ ] New factory skills in `.agent/skills/`? → Update "Project Overview" diagram
> - [ ] Workflow process changed? → Update "Skill Creation Workflow" section
> - [ ] **skill-creator outdated?** → Update `.agent/skills/skill-creator/SKILL.md` too!
> 
> **How to Self-Evolve:**
> ```bash
> # 1. List current blueprint skills
> ls blueprint/skills/
> 
> # 2. Check for new make targets
> grep -E "^[a-z].*:" Makefile | head -20
> 
> # 3. Update this file AND skill-creator if needed
> ```
> 
> ⚠️ **If you skip this step, the skill becomes outdated and USELESS.**

## Language Requirements

> All skill files must be in English. See [LANGUAGE.md](file://.agent/rules/LANGUAGE.md).

## Project Overview

**Purpose**: The Factory separates the **creator** (skill-creator) from the **products** (skills in `blueprint/`).

```
antigravity-factory/
├── .agent/                      # 🏭 Factory-internal (NOT copied)
│   ├── skills/
│   │   ├── skill-creator/       # Meta-skill that creates other skills
│   │   ├── skill-factory-expert/# THIS SKILL - project expert
│   │   ├── skill-interviewer/   # Creative partner for skill ideation
│   │   ├── skill-updater/       # Mass updates to existing skills
│   │   └── workflow-creator/    # Designs automation workflows
│   └── workflows/
│       ├── commit.md            # Pre-commit checks + changelog
│       ├── push.md              # Merge + push pipeline
│       └── self-evolve.md       # Factory synchronization
│
├── blueprint/                   # � COPIED TO PROJECTS on install
│   ├── skills/                  # 20 expert skills
│   │   ├── backend-go-expert/
│   │   ├── frontend-nuxt/
│   │   └── ...
│   ├── workflows/               # Project workflows (10)
│   │   ├── doc-cleanup.md       # Document cleanup
│   │   ├── refactor.md          # Refactoring workflow
│   │   ├── new-project.md       # Start new project
│   │   ├── new-feature.md       # Add feature
│   │   ├── implement.md         # Implementation phase
│   │   ├── deploy.md            # Deployment
│   │   ├── debug.md             # Bug investigation
│   │   ├── archive.md           # Close work unit
│   │   ├── status.md            # Project status
│   │   └── qa.md                # QA cycle
│   ├── rules/                   # Team structure
│   │   ├── TEAM.md
│   │   └── PIPELINE.md
│   └── standards/               # Protocols
│       ├── TDD_PROTOCOL.md
│       ├── GIT_PROTOCOL.md
│       └── ...
│
├── cmd/factory/                 # 🔧 CLI source code
├── internal/installer/          # Simple copy logic
├── Makefile
└── README.md
```

> [!TIP]
> **Always read `AGENTS.md` first!** It contains full project context, CLI commands, and development rules.

## Core Concepts

### 1. Skill Categories
- **Factory Skills** (`.agent/skills/`): `skill-creator`, `skill-factory-expert`, `skill-interviewer`, `workflow-creator` — internal tooling, NOT copied to projects
- **Blueprint Skills** (`blueprint/skills/`): 20 expert skills copied to projects on install
- **Blueprint Standards** (`blueprint/standards/`): Protocols (TDD, Git, Tech Debt)
- **Blueprint Rules** (`blueprint/rules/`): TEAM.md, PIPELINE.md
- **Blueprint Workflows** (`blueprint/workflows/`): doc-cleanup, refactor

### 2. Skill Structure
Every skill follows this pattern:
```
<skill-name>/
├── SKILL.md          # Brain: Logic, Decisions, Workflow (<500 lines)
├── scripts/          # Hands: Python/Bash for execution
├── resources/        # Tools: Templates, Configs
├── examples/         # Demos: Usage examples
└── references/       # Library: Docs, Cheatsheets
```

### 3. Design Philosophy
1. **Concise is Key**: SKILL.md must be under 500 lines
2. **Progressive Disclosure**: Metadata → SKILL.md → Scripts/References
3. **IDE Awareness**: Absolute paths, `task_boundary` for long tasks
4. **Dual-Write Pattern**: Drafts in `brain/`, finals in `project/docs/`

### 4. Project Docs Convention

> [!CAUTION]
> **All skills MUST use `project/docs/` NOT `docs/`!**
> 
> - ✅ Correct: `project/docs/features/`, `project/docs/architecture/`
> - ❌ Wrong: `docs/features/`, `docs/architecture/`

## Makefile Commands

| Command | Description |
|---------|-------------|
| `make install` | **Full install**: validate-all → build-factory → install-factory → completions |
| `make validate SKILL=<name>` | Validate a single skill in `blueprint/skills/` |
| `make validate-all` | Validate all skills in `blueprint/skills/` |
| `make generate-team` | Regenerate `blueprint/rules/TEAM.md` from skill descriptions |
| `make build-factory` | Build the `factory` CLI binary to `bin/factory` |
| `make install-factory` | Install CLI to `/usr/local/bin/factory` |
| `make test` | Run all Go tests |
| `make lint` | Run linters |
| `make uninstall` | Remove factory CLI |

## Factory CLI

The factory includes a Go CLI (`factory`) for managing blueprints in workspaces:

```bash
factory install     # Copy blueprint to .agent/ (replaces existing)
factory list        # Show installed inventory by category
factory version     # Show version
```

> [!NOTE]
> CLI reads config from `~/.config/factory/config.yaml`

## Skill Creation Workflow

### Phase 1: Design
Before creating a skill, answer:
1. **What is the Trigger?** What user intent activates this skill?
2. **What is the Decision Tree?** Single path or multiple?
3. **What Resources are needed?** Scripts, templates, references?

### Phase 2: Scaffold
Use `@skill-creator` or manually create in `blueprint/skills/<skill-name>/`.

### Phase 3: Refine
1. Fill the **Decision Tree** in SKILL.md
2. Write the **Workflow** with clear phases
3. **Adapt the checklist** in `references/checklist.md`
4. Move large code examples to `examples/`
5. **Enforce Handoff Protocol**: Ensure "Draft -> Approved" status change step

### Phase 4: Verify
```bash
make validate SKILL=<skill-name>
```
Checks: frontmatter, length (<500), team sections, checklist customization.

### Phase 5: Install
```bash
cd your-project
factory install
```
Physically copies `blueprint/` → `.agent/` in the project.

## Key Files

### Validation Script
`Path: .agent/skills/skill-creator/scripts/validate_skill.py`
- Validates SKILL.md frontmatter
- Checks line count (<500)
- Ensures Team Collaboration & When to Delegate sections exist
- Verifies checklist in references/

### Scripts
`Path: .agent/skills/skill-creator/scripts/`
- `init_skill.py` — Creates skill skeleton
- `validate_skill.py` — Validates SKILL.md against standards
- `validate_blueprint.py` — Validates blueprint consistency (presets, TEAM.md sync)
- `bump_versions.py` — Auto-bumps skill versions based on git diff
- `add_config_awareness.py` — Adds config awareness to skills
- `add_mcp_awareness.py` — Adds MCP tool awareness to skills

### Standards (Shared Protocols)
- `Path: blueprint/standards/TDD_PROTOCOL.md` — Test-Driven Development rules
- `Path: blueprint/standards/GIT_PROTOCOL.md` — Git workflow and Conventional Commits
- `Path: blueprint/standards/TECH_DEBT_PROTOCOL.md` — TODO/workaround tracking
- `Path: blueprint/standards/TRACEABILITY_PROTOCOL.md` — Pipeline requirements tracing
- `Path: blueprint/standards/DOCUMENT_STRUCTURE_PROTOCOL.md` — Document lifecycle

### Team Registry
`Path: blueprint/rules/TEAM.md`
- Auto-generated via `make generate-team`
- Lists all skills with descriptions

## Anti-Patterns

❌ **NEVER** create skills directly in `~/.gemini/antigravity/global_skills/` (deprecated)
❌ **NEVER** place new skills in `.agent/skills/` (reserved for factory tooling)
❌ **NEVER** embed large code blocks (>10 lines) in SKILL.md

✅ **ALWAYS** create skills in `blueprint/skills/`
✅ **ALWAYS** use `factory install` to deploy to projects
✅ **ALWAYS** customize checklist for the skill's domain
✅ **ALWAYS** add Team Collaboration and When to Delegate sections

## Content Organization

**SKILL.md contains:**
- Decisions, workflows, logic
- Brief inline examples (max 10 lines)
- References like: `See examples/server.py`

**examples/ contains:**
- Full working code
- Configuration samples

**references/ contains:**
- Cheatsheets
- External docs
- Troubleshooting guides

## Team Collaboration

Skills must include team awareness:

```markdown
## Team Collaboration
- **Role**: `@skill-name` (Description of collaboration)

## When to Delegate
- ✅ **Delegate to `@skill-name`** when: <condition>
- ⬅️ **Return to `@skill-name`** if: <condition>
```

See `blueprint/rules/TEAM.md` for the full roster.

## Core Pipeline

The Discovery-to-Delivery pipeline flows through these phases:

```
idea-interview → product-analyst → bmad-architect → tech-spec-writer → implementation → delivery
```

**Core path:** 5 mandatory skills
**Optional paths:**
- TMA/Bot: `@telegram-mechanic`, `@tma-expert`
- CLI: `@cli-architect`, `@tui-charm-expert`  
- Design: `@ux-designer`, `@ui-implementor`
- Hosting: `@timeweb-sysadmin`

## Current Blueprint Skills (20)

| Skill | Focus |
|-------|-------|
| `idea-interview` | Discovery phase, extract project info |
| `feature-fit` | Analyzes new features for EXISTING projects |
| `product-analyst` | Vision, Roadmap, Specs |
| `tech-spec-writer` | Converts architecture into detailed tech specs |
| `bmad-architect` | DDD, Context Maps, API Contracts |
| `backend-go-expert` | Go 1.25+, Clean Architecture, DDD |
| `frontend-nuxt` | Nuxt 4, TailwindCSS, SSR |
| `tma-expert` | Telegram Mini Apps |
| `telegram-mechanic` | Bot API, Webhooks, initData |
| `cli-architect` | Cobra, Viper, POSIX CLI |
| `tui-charm-expert` | BubbleTea, Lipgloss |
| `mcp-expert` | MCP servers (Go) |
| `ux-designer` | Design systems, tokens |
| `ui-implementor` | Tailwind, shadcn/ui |
| `qa-lead` | E2E, API, UI testing |
| `devops-sre` | Docker, CI/CD, deployments |
| `timeweb-sysadmin` | Timeweb Cloud, VPS |
| `project-bro` | Project awareness |
| `refactor-architect` | Codebase analysis, modular refactoring |
| `doc-janitor` | Document cleanup, lifecycle enforcement |
| `debugger` | Systematic 7-step bug investigation |

## When to Delegate

- ✅ **Delegate to `@skill-creator`** when: Creating a new skill
- ⬅️ **Return from `@skill-creator`** after: Skill is scaffolded
- 🤝 **Coordinate with blueprint skills** when: Understanding their capabilities

## Antigravity Best Practices

- Use `task_boundary` when performing multi-step operations
- Use `notify_user` for user review checkpoints
- Always use **absolute paths** in scripts and documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
