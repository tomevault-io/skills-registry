---
name: context-checkpoint
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Context Checkpoint

## Commands

- `/checkpoint save` — Capture current session state
- `/checkpoint save <label>` — Save with a descriptive label
- `/checkpoint load` — Load the most recent checkpoint
- `/checkpoint load <label>` — Load a specific labeled checkpoint
- `/checkpoint list` — List all saved checkpoints

## Save Procedure

1. Scan conversation for key decisions, file changes, and state
2. Generate structured checkpoint markdown
3. Write to `.claude/checkpoints/{timestamp}-{label}.md`
4. Confirm path and summary to user

## Checkpoint Template

The checkpoint file includes these sections:

- Checkpoint metadata (date, session description)
- Decisions Made (with rationale)
- Files Modified (path, what changed, why)
- Files Created (path, purpose)
- Current State (what's working, what's partial)
- Blockers (what's preventing progress)
- Next Steps (ordered action items)
- Environment State (branch, uncommitted changes, PAC profile, solution version)
- Resume Prompt (ready-to-paste prompt for a new session to pick up)

## Load Procedure

1. Find the requested checkpoint file
2. Read and parse all sections
3. Present summary to user
4. Verify files still exist and haven't changed
5. Use the Resume Prompt to continue work

## Storage

- Location: `.claude/checkpoints/`
- Format: `{YYYYMMDD-HHMM}-{slugified-label}.md`

## MCMAP-Specific State

Always capture: solution version, active PAC profile, in-progress imports, unpacked solution path, recent validation results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
