---
name: claude-md-authoring
description: Write and optimize CLAUDE.md files. Use when creating, improving, or reviewing CLAUDE.md for best practices, or troubleshooting ignored instructions. Also applies to AGENTS.md. Use when this capability is needed.
metadata:
  author: hudrazine
---

# CLAUDE.md Authoring Guide

## Core Concept

CLAUDE.md onboards Claude to your codebase at the start of every session. Claude Code injects it with a system reminder that tells Claude to ignore content if not highly relevant to the current task.

**Implication**: Include only universally applicable content. Task-specific instructions belong in separate files.

## What to Include

Cover three areas concisely:

- **WHY**: Project purpose and goals
- **WHAT**: Tech stack, project structure, key directories (especially for monorepos)
- **HOW**: Commands to build, test, verify changes; preferred tools (e.g., `bun` vs `node`)

## Key Principles

### Less Instructions Is More

Frontier thinking models reliably follow ~150-200 instructions. Claude Code's system prompt uses ~50. The CLAUDE.md should minimize instruction count to stay within this budget.

**Target**: <300 lines total, ideally <100 lines for the root file.

### Universal Applicability

Every line should apply to most tasks. Remove content that only matters for specific scenarios.

**Bad**: "When creating database schemas, use snake_case for column names"
**Good**: Move schema guidelines to `agent_docs/database_schema.md`

### Progressive Disclosure

Store task-specific instructions in separate files. Reference them from CLAUDE.md:

```
agent_docs/
├── building_the_project.md
├── running_tests.md
├── code_conventions.md
└── database_schema.md
```

In CLAUDE.md, list these with brief descriptions so the operating Claude instance reads them only when relevant.

**Prefer pointers to copies**: Reference `file:line` instead of embedding code snippets that become stale.

### Don't Replace Linters

Never include code style guidelines in CLAUDE.md. Use actual linters and formatters instead:

- LLMs are slow and expensive compared to deterministic tools
- Style rules add irrelevant instructions, degrading performance
- Claude learns patterns from existing code via in-context learning

**Better approach**: Set up a Stop hook that runs formatters and presents errors to Claude.

### Deliberate Authoring

CLAUDE.md affects every phase of every workflow—it's the highest leverage point in the harness. Each line should justify its inclusion. Before adding content, ask: "Does this apply universally? Is it worth the instruction cost?"

## Workflow

**Creating new CLAUDE.md:**

1. Identify universally applicable information (WHY, WHAT, HOW)
2. Write concise descriptions for each area
3. Move task-specific content to `agent_docs/` files
4. List agent_docs files with brief descriptions in CLAUDE.md
5. Review: Does every line apply to most tasks?

**Optimizing existing CLAUDE.md:**

1. Count lines and instructions (target <300 lines)
2. Identify task-specific content → move to separate files
3. Remove code style guidelines → use linters instead
4. Check for outdated code snippets → replace with file references
5. Verify universal applicability of remaining content

## References

- **Detailed best practices**: See [references/best-practices.md](references/best-practices.md)
- **Progressive disclosure examples**: See [references/progressive-disclosure.md](references/progressive-disclosure.md)
- **Common anti-patterns**: See [references/anti-patterns.md](references/anti-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hudrazine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
