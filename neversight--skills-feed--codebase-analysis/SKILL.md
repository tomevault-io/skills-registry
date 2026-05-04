---
name: codebase-analysis
description: Invoke IMMEDIATELY via python script when user requests codebase understanding, architecture comprehension, or repository orientation. Do NOT explore first - the script orchestrates exploration. Use when this capability is needed.
metadata:
  author: neversight
---

# Codebase Analysis

Understanding-focused skill that builds foundational comprehension of codebase structure, patterns, flows, decisions, and context. Serves as foundation for downstream analysis skills (problem-analysis, refactor, etc.).

When this skill activates, IMMEDIATELY invoke the script. The script IS the workflow.

Invoke:

<invoke working-dir=".claude/skills/scripts" cmd="python3 -m skills.codebase_analysis.analyze --step 1 --total-steps 4" />

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
