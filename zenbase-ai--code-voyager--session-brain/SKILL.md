---
name: session-brain
description: | Use when this capability is needed.
metadata:
  author: zenbase-ai
---

# Session Brain

Persistent per-repo working memory stored under `.claude/voyager/`.

## State Files

| File | Purpose |
|------|---------|
| `brain.json` | Canonical structured state (validated against schema) |
| `brain.md` | Human-readable summary for quick reference |
| `episodes/` | Per-session summaries for history |

## How It Works

Session Brain updates **automatically** via hooks:
- **SessionStart**: Injects current brain context + repo snapshot
- **PreCompact/SessionEnd**: Persists changes to brain.json and creates episode

You don't need to manually update the brain—just work normally and it tracks context.

## Reading brain.json

When exploring brain.json, use `jq` to query specific sections instead of reading the whole file:

```bash
# Current context (goal, plan, questions)
jq '.working_set' .claude/voyager/brain.json

# Decision history
jq '.decisions' .claude/voyager/brain.json

# Recent progress
jq '.progress' .claude/voyager/brain.json

# Project overview
jq '.project' .claude/voyager/brain.json
```

For a quick human-readable summary, read `.claude/voyager/brain.md` instead.

## Answering User Questions

When users ask about context, read and interpret the brain files:

### "Resume" / "What were we doing?"

1. Read `.claude/voyager/brain.md` for quick context
2. Check `brain.json` → `working_set.current_goal` and `working_set.current_plan`
3. Summarize: current goal, plan progress, any open questions

### "What's next?"

1. Read `brain.json` → `working_set.current_plan`
2. Check `progress.recent_changes` to see what's done
3. Suggest the next uncompleted step from the plan

### "Why did we decide X?" / "What was the rationale?"

1. Read `brain.json` → `decisions` array
2. Find matching decision by keyword
3. Return the `rationale` and `implications`

### "What are the open questions?"

1. Read `brain.json` → `working_set.open_questions`
2. Also check `working_set.risks` for blockers

## Recording Decisions

When the user says "remember this decision: ..." or explicitly wants to record a decision:

1. Read current `.claude/voyager/brain.json`
2. Append to the `decisions` array:
   ```json
   {
     "when": "<ISO timestamp>",
     "decision": "<what was decided>",
     "rationale": "<why>",
     "implications": ["<what this affects>"]
   }
   ```
3. Write updated `brain.json`

Note: `brain.md` will be re-rendered automatically on the next session end or compaction.

## Brain Schema Overview

See `schemas/brain.schema.json` for full schema. Key sections:

- **project**: Stable project understanding (summary, stack, key commands)
- **working_set**: Current context (goal, plan, open questions, risks)
- **decisions**: Append-only decision log with rationale
- **progress**: Recent changes and completed milestones
- **signals**: Metadata (last session ID, timestamp)

## Technical Details

See `reference.md` for:
- Full file structure
- Update flow details
- Manual operations (reset, view history)
- Graceful degradation behavior

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zenbase-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
