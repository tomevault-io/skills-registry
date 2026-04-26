---
name: human-checkpoint
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Human Checkpoint (HITL sign-off)

Goal: make human approvals explicit and auditable, so downstream units can safely proceed.

This skill is intentionally simple: it standardizes how a human signs off in `DECISIONS.md`.

## Inputs

Required:
- `DECISIONS.md`

Optional:
- `UNITS.csv` (to confirm which checkpoint is currently blocked, e.g., `C1`/`C2`)
- `STATUS.md` (to confirm the current checkpoint)

## Outputs

- `DECISIONS.md` (updated checkbox + short sign-off note)

## Procedure

1. Identify the blocked checkpoint
   - Check the runner message (or `STATUS.md`), or inspect `UNITS.csv` for the first `owner=HUMAN` unit that is `BLOCKED`.

2. Open `DECISIONS.md` and find the approvals checklist
   - Look for a line like: `- [ ] Approve C2 ...`

3. Review the artifacts required by that checkpoint
   - The pipeline doc (`pipelines/*.pipeline.md`) usually lists what to review (e.g., protocol, outline, module plan).
   - If the checkpoint block is missing, add a short checklist into `DECISIONS.md` (what you reviewed).

4. Approve
   - Tick the checkbox: `- [x] Approve C*`
   - Append a short note under the checkpoint block (recommended):
     - Date
     - Artifacts reviewed
     - What you approved + constraints (if any)
     - Signed by

## Acceptance

- The correct `Approve C*` checkbox in `DECISIONS.md` is ticked (`[x]`).
- Any added sign-off note does not silently expand scope or contradict the run goal.

## Troubleshooting

### The approvals checklist is missing in `DECISIONS.md`

Fix:
- Run `pipeline-router` for the relevant checkpoint block, or add a minimal block manually:

```markdown
## Approvals (check to unblock)
- [ ] Approve C2
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
