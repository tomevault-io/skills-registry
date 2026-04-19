---
name: taskforce-coding-team
description: Run the taskforce coding team workflow: execute the taskforce-coding-buddy skill with codex-ultra, execute the taskforce-review-report skill with gemini-ultra, and loop based on the report STATUS. Use when asked to follow taskforce_coding_team.md or to orchestrate this coding+review loop. Use when this capability is needed.
metadata:
  author: mkxultra
---

# Taskforce Coding Team

Act as the operator and drive the workflow steps below.

## Workflow

1. Run coding skill
   - Use `mcp__acm__run` to execute the `taskforce-coding-buddy` skill.
   - Model: `codex-ultra`
   - Session: new on first run, then reuse the most recent `session_id`.

2. Run review skill
   - Use `mcp__acm__run` to execute the `taskforce-review-report` skill.
   - Model: `gemini-ultra`
   - Session: new on first run, then reuse the most recent `session_id`.

3. Triage review result
   - Check the report `STATUS`.
   - If `APPROVE`, stop.
   - If `CHANGE_REQUEST`, return to step 1.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mkxultra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
