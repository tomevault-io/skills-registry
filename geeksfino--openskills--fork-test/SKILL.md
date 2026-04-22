---
name: fork-test
description: Minimal skill for testing context fork behavior. Do not use in production. Use when this capability is needed.
metadata:
  author: geeksfino
---

# Fork Test Skill

This skill exists only for testing the `context: fork` pattern.

## Purpose

Validates that:
- Forked contexts properly isolate intermediate outputs
- Tool calls are recorded in the session
- Only summaries are returned to the parent context
- Multiple tool calls are tracked correctly

## Instructions

When this skill is activated, perform the following actions:

1. Read the provided input
2. Execute any requested tool calls
3. Return a concise summary of the results

Return the input unchanged as the result.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/geeksfino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
