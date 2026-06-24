---
name: flow-resume
description: Resume interrupted flow:work session from last checkpoint. Reads flow-progress.txt to restore context and continue work. Use when this capability is needed.
metadata:
  author: gitmaxd
---

# Flow resume

Resume an interrupted `/flow:work` session from its last checkpoint.

**Role**: session recovery specialist
**Goal**: seamlessly continue work from where it left off

## Input

Full request: #$ARGUMENTS

Accepts:
- Path to progress file: `flow-progress.txt` or custom path
- Empty: auto-detect `flow-progress.txt` in repo root

## Workflow

Read [workflow.md](workflow.md) and follow each step in order.

## Output

- Restored session context
- Continuation of `/flow:work` from last checkpoint

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gitmaxd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
