---
name: setup
description: Scaffold per-repo configuration for all other skills. Run once per project before using any other skill. Configures issue tracker, triage labels, doc paths, test framework, and creates CONTEXT.md and initial ADR. Supports Claude Code, Cursor, Windsurf, Cline, GitHub Copilot, Codex, Aider, and any AI tool. Use when this capability is needed.
metadata:
  author: novvoo
---

# Initialize Project

> "Start as you mean to go on."

## Philosophy

All other skills depend on shared configuration and context. This skill creates that foundation. Run once per project. Takes 2 minutes.

## Workflow

### Step 1: Ask Configuration Questions

Use defaults if user doesn't want to decide:

1. **AI Tools** — Which tools? (Claude Code, Cursor, Windsurf, Cline, Copilot, Codex, Aider, Gemini CLI, OpenCode, Copilot CLI, Codex CLI, Other)
2. **Issue Tracker** — GitHub Issues (default) / Linear / Local Files
3. **Triage Labels** — `bug`, `feature`, `enhancement`, `tech-debt`, `question`
4. **Docs Path** — `docs/` (default), `docs/adr/`, `docs/handoff/`
5. **Context File** — `CONTEXT.md` at project root (default)
6. **Test Framework** — What framework? (jest, pytest, go test, etc.)
7. **Code Style** — Project-specific rules?

### Step 2: Create Files

**Always create:**
- `CONTEXT.md` — Domain context and shared language
- `docs/adr/0001-initial-setup.md` — Initial ADR

**Tool-specific (based on Step 1):**

| Tool | File |
|------|------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursor/rules/integrated-guidelines.mdc` |
| Windsurf | `.windsurfrules` |
| Cline | `.clinerules` |
| GitHub Copilot | `.github/copilot-instructions.md` |
| Codex | `AGENTS.md` |
| Aider | `.aider.conf.yml` |
| Gemini CLI | `GEMINI.md` |
| OpenCode | `.opencode/instructions.md` |
| Copilot CLI | `.copilot-cli/instructions.md` |
| Codex CLI | `.codex/instructions.md` |
| Other | `SYSTEM_PROMPT.md` (copy content to tool's custom instructions) |

### Step 3: Write CONTEXT.md

```markdown
# Project Context

## Domain Language
- [Term 1]: [definition]
- [Term 2]: [definition]

## Module Map
- [Module A]: [responsibility]
- [Module B]: [responsibility]

## Architecture Decisions
- See `docs/adr/` for all decisions

## Code Style
- [Rule 1]
- [Rule 2]
```

### Step 4: Write Initial ADR

```markdown
# ADR 0001: Initial Project Setup

## Status
Accepted

## Context
Setting up AI Engineering Mastery skills for this project.

## Decision
- Issue tracker: [choice]
- AI tools: [choices]
- Test framework: [choice]

## Consequences
- All skills will use this configuration
- CONTEXT.md is the source of truth for domain language
```

### Step 5: Output Summary

```
Setup Complete!

Configuration:
- Issue Tracker: [choice]
- AI Tools: [choices]
- Docs Path: [path]
- Test Framework: [choice]

Files Created:
- CONTEXT.md
- docs/adr/0001-initial-setup.md
- [tool-specific files]

Available Skills:
  /brainstorm  — Design exploration with HARD-GATE
  /grill      — Deep alignment before coding
  /tdd        — Test-driven development
  /diagnose   — Systematic debugging
  /architect  — Architecture improvement
  /zoom-out   — System-level context
  /to-prd     — Create PRD
  /to-issues  — Break plan into issues
  /caveman    — Compressed communication
  /handoff    — Session handoff
  /verify      — Evidence-based completion verification
  /review      — Code review (request + receive)

Quick Start:
1. Start with /grill to align on your first task
2. Use /tdd to implement with confidence
3. End with /handoff to save your progress
```

## Anti-Patterns

- **Over-configuring** — Use defaults, adjust later
- **Skipping setup** — Other skills depend on this
- **Not updating CONTEXT.md** — It should evolve with the project

## Integration

- All other skills depend on this configuration
- Run `/setup` before any other skill
- Re-run if configuration changes significantly

---
> Source: [novvoo/ai-engineering-mastery](https://github.com/novvoo/ai-engineering-mastery) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
