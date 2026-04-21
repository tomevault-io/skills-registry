---
name: session-end
description: Combined workflow for ending an AI collaboration session. Runs reflect to capture learnings, consolidates memories, and summarizes session work. Use before ending any work session to preserve context. Use when this capability is needed.
metadata:
  author: robabby
---

# Session End

Combined shutdown workflow for AI collaboration sessions.

## What It Does

1. **Reflect** - Review session and identify memories to store
2. **Consolidate** - Create memory files for important learnings
3. **Summarize** - Provide session summary and note unfinished work

## Workflow

1. Review session work
   - What was accomplished?
   - What decisions were made?
   - What was learned?
   - What remains unfinished?

2. Identify memories to store
   - Episodic: Events, breakthroughs, frustrations
   - Semantic: Facts learned, knowledge gained
   - Procedural: Patterns discovered, processes established
   - Strategic: Decisions made, plans formed

3. Create memory files
   - Generate properly formatted memory files
   - Assess importance and concepts
   - Store in appropriate folders

4. Update session log (optional)
   - Create session log entry
   - Link to memories created

5. Note unfinished work
   - What needs follow-up?
   - Any blockers or pending decisions?

6. Provide session summary
   - Work completed
   - Memories stored
   - Next steps

## Parameters

- `$ARGUMENTS` (optional): Focus area for reflection

## Output Format

```
## Session Ended

### Work Completed
- {accomplishment}
- {accomplishment}

### Memories Stored
| Type | Title | Importance |
|------|-------|------------|
| {type} | {title} | {score} |

### Unfinished / Follow-up
- {item}

### Session Stats
- Duration: ~{time}
- Files created/modified: {count}
- Memories stored: {count}

---

Context preserved. See you next session.
```

## Example

User: `/session-end`

Response:
"## Session Ended

### Work Completed
- Brainstormed AI Ready Vault product concept
- Evolved from vault templates to composable skills system
- Created 6 project documentation files
- Defined skills library architecture
- Created 11 skill definitions

### Memories Stored
| Type | Title | Importance |
|------|-------|------------|
| Strategic | AI Ready Vault Product Vision | 0.9 |
| Strategic | Target Audience Decision | 0.7 |
| Procedural | Teaching Pattern for AI Concepts | 0.6 |
| Semantic | Skills Library Concept | 0.8 |

### Unfinished / Follow-up
- Skills need testing and refinement
- Website scroll experience not yet implemented
- Vault tiers need finalization

### Session Stats
- Duration: ~2 hours
- Files created: 17 (6 project docs, 11 skills)
- Memories stored: 4

---

Context preserved. See you next session."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
