---
name: problem-analysis
description: Invoke IMMEDIATELY via python script when user requests problem analysis or root cause investigation. Do NOT explore first - the script orchestrates the investigation. Use when this capability is needed.
metadata:
  author: neversight
---

# Problem Analysis

Root cause identification skill. Identifies WHY a problem occurs, NOT how to fix
it.

## Invocation

<invoke working-dir=".claude/skills/scripts" cmd="python3 -m skills.problem_analysis.analyze --step 1 --total-steps 5" />

Do NOT explore or analyze first. Run the script and follow its output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
