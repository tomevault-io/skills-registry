---
name: skill-assessment-on-completion
description: After tasks with 3+ tool calls, evaluates whether to create a reusable skill, snippet, or n8n workflow for future automation. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Skill Assessment on Completion

## Trigger
After completing any task that took more than 3 tool calls.

## Decision Tree
Will this exact task happen again?
- NO → Done, no skill needed
- YES → Could inputs change while steps stay same?
  - NO → Save as snippet, not full skill
  - YES → Create full skill

## Skill Goes to n8n Instead If
- Runs on schedule (not on-demand)
- Needs to run when Warp isn't open
- Involves webhook triggers

## Skip When
- One-off troubleshooting
- Answering a question
- User-specific (no generalization possible)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
