---
name: wave-start
description: Execute the next wave of tasks. Use when starting implementation of the next batch of tasks. Use when this capability is needed.
metadata:
  author: antibioticvz
---
# Wave Execution

Check native Tasks (TaskList) for the next incomplete wave.

For independent tasks within the wave: launch parallel sub-agents via the
Task tool, each following the protocol in CLAUDE.md.

For tasks sharing files: run them sequentially.

After all tasks complete: run build, update task status, summarize results.

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/antibioticvz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
