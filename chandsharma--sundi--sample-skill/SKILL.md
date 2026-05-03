---
name: sample-skill
description: Deterministic sample skill for lint and inventory. Use when this capability is needed.
metadata:
  author: chandsharma
---

# Sample Skill

## Goal
Provide a deterministic example skill that exercises the skills lint and inventory system.

## When to use (triggers)
- When validating the skills repository layout.
- When demonstrating the skill meta test-case format.

## Inputs / Outputs (schemas)
- Input: short text prompt (string)
- Output: brief structured acknowledgment (string)

## Procedure (steps)
1) Read the input text.
2) Respond with a short acknowledgment that includes the word "pong" and a status label.

## Failure modes + recovery
- If the input is empty, return a status indicating missing input.
- If the response would omit required keywords, retry with a minimal template.

## Examples
Input: "ping"
Output: "pong | status=ok"

## Test vectors
- Input: "ping"
  Expected contains: ["pong", "status=ok"]

## Evidence + version history
- 0.1.0: Initial deterministic sample for Sprint 11.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chandsharma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
