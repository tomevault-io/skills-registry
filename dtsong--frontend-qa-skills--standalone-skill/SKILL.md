---
name: skill-name
description: > Use when this capability is needed.
metadata:
  author: dtsong
---

# SKILL_NAME

## Inputs
- `required_input`: DESCRIPTION
- `optional_input` (optional): DESCRIPTION

## Procedure

Step 1: Gather context.
  - Read RELEVANT_CONFIG_FILES
  - Record constraints for subsequent steps

Step 2: PRIMARY_ACTION.
  - IMPERATIVE_INSTRUCTION
  - If CONDITION → BRANCH_A. Otherwise → BRANCH_B.
  - Note: EDGE_CASE_CONTEXT — explains why this matters for correct results.

Step 3: Verify output.
  - Check each item in the Output Contract
  - If any check fails → fix and re-verify

## Output Format

```json
{
  "result": "EXAMPLE_VALUE",
  "confidence": "high|medium|low",
  "details": []
}
```

## References
| File | Load When | Contains |
|------|-----------|----------|
| `references/CHECKLIST.md` | Step 2 if CONDITION | Detailed checklist for X |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtsong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
