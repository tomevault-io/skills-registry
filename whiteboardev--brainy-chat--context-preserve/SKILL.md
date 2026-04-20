---
name: context-preserve
description: Preserves session context when context window fills. Triggers at configurable thresholds (50%/75%). Captures objective, progress, technical details, decisions, blockers, and next steps. Use when this capability is needed.
metadata:
  author: whiteboardev
---

# Context Window Preservation

Automatically save session state before context window fills.

## State Machine

```
START → CHECK_CONTEXT → EVALUATE_THRESHOLD
     ├→ SKIP (< 50%) ───────────────────────────→ END
     ├→ QUICK (50-75%) → COLLECT_SUMMARY → STORE → END
     └→ FULL (> 75%) ──→ COLLECT_DETAILED → STORE → END
```

## Thresholds

| Level | Threshold | Action | Sections |
|-------|-----------|--------|----------|
| Skip | < 50% | None | - |
| Quick | 50-75% | Summary | 3 (objective, progress, next) |
| Full | > 75% | Detailed | 6 (all) |

## Execution

### Path A: Subagent (preferred)

```
Task(
  subagent_type: "summarizer",
  prompt: "Check context usage for project PROJECT_ID and preserve if needed"
)
```

### Path B: Direct scripts

```bash
# Check usage
bun .opencode/skills/context-preserve/scripts/check-context-usage.ts PROJECT_ID

# Store (after LLM collects content)
bun .opencode/skills/context-preserve/scripts/store-preservation.ts PROJECT_ID "CONTENT" quick|full
```

## Commands

### Check Usage

```bash
bun .opencode/skills/context-preserve/scripts/check-context-usage.ts PROJECT_ID [--tokens N] [--quiet]
```

Exit codes:
- `2` = skip (healthy)
- `3` = quick preservation needed
- `4` = full preservation needed

### Store Preservation

```bash
bun .opencode/skills/context-preserve/scripts/store-preservation.ts PROJECT_ID "CONTENT" TYPE [--quiet]
```

## Templates

### Quick Preservation (50-75%)

```markdown
## Session Context

### Objective
[One sentence: what we're working on]

### Progress
- Completed: [list]
- In progress: [current work]

### Next Steps
1. [Specific action]
2. [Specific action]
```

### Full Preservation (> 75%)

```markdown
## Session Context

### Objective
[What task/problem we're working on and original goals]

### Progress
- Completed: [detailed list with outcomes]
- In progress: [current work with status]
- Pending: [remaining items]

### Technical Details
- Project: [path, structure notes]
- Files modified: [list with purposes]
- Patterns discovered: [architectural notes]
- Dependencies: [relevant packages/configs]

### Decisions
- [Decision]: [Rationale]
- [Trade-off considered]: [Choice made and why]

### Blockers
- [Issue]: [Status and findings]
- [Error encountered]: [Resolution or workaround]

### Next Steps
1. [Specific action with context]
2. [Specific action with context]
3. [Follow-up items identified]
```

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `BRAINY_API_URL` | `http://localhost:3000` | API endpoint |
| `BRAINY_PROJECT_ID` | `3afb861a-...` | Default project |
| `CTX_SKIP_THRESHOLD` | `50` | % below which no action |
| `CTX_QUICK_THRESHOLD` | `75` | % threshold for quick vs full |
| `CTX_WINDOW_SIZE` | `128000` | Estimated context window |

## Workflow

1. **Agent detects** context filling up (or user requests)
2. **Check usage**: `check-context-usage.ts` returns level
3. **If skip**: Done
4. **If quick/full**: LLM collects relevant info using template
5. **Store**: `store-preservation.ts` saves to memory
6. **Continue**: Session can proceed or be resumed later

## Recovery

To resume from preserved context:

```bash
bun .opencode/skills/memory-retrieve/scripts/retrieve-memories.ts PROJECT_ID "context preservation" --limit 1
```

This fetches the most recent preservation memory for context restoration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whiteboardev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
