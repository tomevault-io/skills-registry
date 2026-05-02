---
name: bo-next-batch
description: Generate next experimental suggestions from current run state. Use when this capability is needed.
metadata:
  author: igreat
---

# BO Next Batch

Use this skill to get candidate experiments.

## Command

```bash
uv run python -m bo_workflow.cli suggest --run-id <RUN_ID> --batch-size <N>
```

Accepts status `initialized` or `running`. No oracle needed — HEBO/BO/random work directly from the design space.

## Return

- list of suggestions with `suggestion_id`
- feature values for each candidate
- reminder to record outcomes later via `bo-record-observation`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/igreat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
