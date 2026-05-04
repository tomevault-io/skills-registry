---
name: deepthink
description: Invoke IMMEDIATELY via python script when user requests structured reasoning for open-ended analytical questions. Do NOT explore first - the script orchestrates the thinking workflow. Use when this capability is needed.
metadata:
  author: neversight
---

# DeepThink

Structured multi-step reasoning for open-ended analytical questions where the
answer structure is itself unknown. Handles taxonomy design, conceptual
analysis, trade-off exploration, and definitional questions.

## Invocation

<invoke working-dir=".claude/skills/scripts" cmd="python3 -m skills.deepthink.think --step 1 --total-steps 14" />

Do NOT explore or analyze first. Run the script and follow its output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
