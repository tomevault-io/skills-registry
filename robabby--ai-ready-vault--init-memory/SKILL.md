---
name: init-memory
description: Initialize the memory system in an AI-ready vault. Creates the folder structure, Memory.md dashboard, and example files needed for cross-session memory persistence. Use when setting up a new vault or adding memory capabilities to an existing vault. Use when this capability is needed.
metadata:
  author: robabby
---

# Init Memory

Create the memory system folder structure and dashboard.

## Memory System Structure

```
Areas/AI/Memory/
├── Memory.md           # Dashboard with DataView queries
├── Episodic/           # Events, breakthroughs, frustrations
├── Semantic/           # Facts, definitions, project details
├── Procedural/         # Patterns, how-tos, workflows
└── Strategic/          # Decisions, approaches, plans
```

## Workflow

1. Confirm target location
   - Default: `Areas/AI/Memory/`
   - Ask user if different location preferred

2. Create folder structure
   - Create parent folders if needed
   - Create Memory/ directory
   - Create four type subdirectories

3. Create Memory.md dashboard
   - Overview of memory system
   - DataView queries for recent/important memories
   - Quick reference for memory types

4. Create example memories (optional)
   - One example in each folder
   - Demonstrates proper format
   - Can be deleted after review

5. Update CLAUDE.md
   - Add memory system documentation
   - Add memory-related instructions

## Memory.md Template

```markdown
---
created: {date}
type: dashboard
tags:
  - memory-system
  - ai
---

# Memory System

Cross-session memory for AI collaboration.

## Memory Types

| Type | Purpose | When to Use |
|------|---------|-------------|
| **Episodic** | Events & experiences | Breakthroughs, frustrations, milestones |
| **Semantic** | Facts & knowledge | Definitions, project details, learned info |
| **Procedural** | Patterns & how-tos | Workflows, techniques, processes |
| **Strategic** | Decisions & plans | Choices made, approaches, rationale |

## Recent Memories

```dataview
TABLE type, importance, concepts
FROM "Areas/AI/Memory"
WHERE type != "dashboard"
SORT created DESC
LIMIT 10
```

## High Importance

```dataview
TABLE type, created, concepts
FROM "Areas/AI/Memory"
WHERE importance >= 0.7
SORT importance DESC
```

## Usage

- `/remember` - Store a new memory
- `/recall {terms}` - Search memories
- `/reflect` - End-of-session consolidation
- `/glean` - Surface patterns across memories
```

## Parameters

- `$ARGUMENTS` (optional): Custom path for memory system (default: `Areas/AI/Memory/`)

## Example

User: `/init-memory`

Response:
"Initializing memory system...

Created:
→ `Areas/AI/Memory/`
→ `Areas/AI/Memory/Memory.md` (dashboard)
→ `Areas/AI/Memory/Episodic/`
→ `Areas/AI/Memory/Semantic/`
→ `Areas/AI/Memory/Procedural/`
→ `Areas/AI/Memory/Strategic/`

Memory system ready. Use `/remember` to store your first memory."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
