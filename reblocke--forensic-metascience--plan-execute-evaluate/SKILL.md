---
name: plan-execute-evaluate
description: Use when working with a disciplined loop: plan first, implement in small diffs, verify with tests/commands, and write a handoff.
metadata:
  author: reblocke
---

## When to use
Use this skill for any non-trivial change (new feature, refactor, pipeline change, bug fix).

## Procedure
1. **Restate the task** in your own words.
2. **Ask clarifying questions only if needed** to avoid wrong behavior.
3. **Write a plan**:
   - steps (numbered)
   - files to read/edit
   - commands to run for verification
4. **Identify risks** (silent failure modes, data assumptions, backwards compatibility).
5. **Execute in small increments**:
   - prefer test-first
   - keep diffs reviewable
   - avoid unrelated changes
6. **Verify**:
   - run listed commands
   - summarize results
7. **Handoff**:
   - if work spans sessions, update `docs/HANDOFF.md`.

## Output format
- Clarifying questions (if any)
- Plan
- Risks
- After execution: verification summary + follow-ups

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reblocke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
