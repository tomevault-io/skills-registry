---
name: session-start
description: Combined workflow for starting an AI collaboration session. Runs hydrate, vault-status, and prompts for session focus. Use at the beginning of any work session to load context and orient. Use when this capability is needed.
metadata:
  author: robabby
---

# Session Start

Combined startup workflow for AI collaboration sessions.

## What It Does

1. **Hydrate** - Load vault context and relevant memories
2. **Vault Status** - Show recent activity and current state
3. **Focus Prompt** - Ask what to work on this session

## Workflow

1. Run hydrate workflow
   - Read CLAUDE.md
   - Load recent session context
   - Surface relevant memories
   - If $ARGUMENTS provided, focus hydration on that topic

2. Run vault-status (abbreviated)
   - Recent file changes
   - Today's daily note status
   - Any pending items from last session

3. Present session context
   - Summarize loaded context
   - Note any unfinished work
   - Highlight relevant memories

4. Ask for session focus
   - "What would you like to focus on today?"
   - Or proceed with topic from $ARGUMENTS

## Parameters

- `$ARGUMENTS` (optional): Topic to focus the session on

## Output Format

```
## Session Started

### Context Loaded
- CLAUDE.md: ✓
- Recent sessions: {count} reviewed
- Memories loaded: {count} relevant

### Vault Status
- Last activity: {when}
- Daily note: {status}
- Recent changes: {summary}

### From Last Session
- {unfinished items or context}

### Relevant Memories
- {memory summaries}

---

What would you like to focus on?
```

## Example

User: `/session-start`

Response:
"## Session Started

### Context Loaded
- CLAUDE.md: ✓ (PKM vault, PARA structure)
- Recent sessions: 2 reviewed (Jan 7, Jan 8)
- Memories loaded: 4 relevant

### Vault Status
- Last activity: 2 hours ago
- Daily note: Present (3 tasks, 1 completed)
- Recent changes: AI Ready Vault project files, memory system

### From Last Session
- AI Ready Vault brainstorming completed
- Skills library concept defined
- Project docs created in Projects/Active/

### Relevant Memories
- **Product Vision** (Strategic, 0.9): Composable skills system, PKM enthusiasts first
- **Skills Library Concept** (Semantic, 0.8): Core/setup/obsidian/bundle categories

---

What would you like to focus on?"

User: `/session-start ai-ready-vault implementation`

Response:
"## Session Started (focused: AI Ready Vault implementation)

### Context Loaded
- CLAUDE.md: ✓
- Project: AI Ready Vault (`~/Workbench/ai-ready-vault`)
- Memories loaded: 6 relevant to ai-ready-vault

### Project Status
- Phase: Skills library implementation
- Last work: Created 11 skill definitions
- Codebase: Next.js 16 project initialized

### Relevant Memories
- **Skills Library Architecture** (Strategic)
- **Teaching Pattern** (Procedural)
- **Target Audience Decision** (Strategic)

---

Ready to continue AI Ready Vault implementation. What's the focus?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
