---
name: interaction-control
description: Clarification, approvals, and progress messaging during tool execution. Use when this capability is needed.
metadata:
  author: traves-theberge
---

# Interaction Control Skill

Use this skill for user confirmations, ambiguity handling, and progress updates.

## Instructions

When user intent is ambiguous:
1. Use `ask_user` with a specific question.
2. Provide concise options when there are multiple valid paths.

When operations are destructive or risky:
1. Confirm intent with `ask_user` before triggering delete/cancel actions.
2. Proceed only after explicit user confirmation.

During long operations:
1. Use `report_progress` for milestone updates.
2. Keep updates factual and short (current step, next step).
3. Avoid noisy progress spam.

When a task asks a question:
1. Surface the question clearly.
2. Route the response through `task_respond` (handled by task skill).

## Tool Reference

- `ask_user`
- `report_progress`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/traves-theberge) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
