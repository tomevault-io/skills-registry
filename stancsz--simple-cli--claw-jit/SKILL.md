---
name: clawjit
description: Use when working with the high-level goal or persona required (e.g., "Audit security", "Fix React bugs").
metadata:
  author: stancsz
---

# Claw JIT Agent Generator
[Simple-CLI AI-Created]

This skill implements the "JIT Agent Layer" of the OpenClaw integration. It:
1.  Analyzes the user's `intent`.
2.  Uses an LLM to generate a specialized `AGENT.md`.
3.  Writes this file to `.simple/workdir/AGENT.md`.
4.  Optionally initializes the memory structure if missing.

## Strategy
Use this skill at the *start* of a complex session when the generic agent persona is insufficient. It is the "bootloader" for a specialized agent session.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stancsz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
