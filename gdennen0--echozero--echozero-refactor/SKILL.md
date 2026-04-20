---
name: echozero-refactor
description: Evaluate and execute refactoring in EchoZero. Use when considering refactoring, evaluating refactor proposals, or when the user asks about refactoring code, code structure changes, or @refactor. Use when this capability is needed.
metadata:
  author: gdennen0
---

# Refactor Command

## When to Use

- Evaluating if refactoring is justified
- Before making structural code changes
- Assessing net complexity impact

## Required Questions

1. **What concrete problem does this solve?**
2. **Is there a simpler fix?**
3. **Can we DELETE instead of reorganize?**
4. **Net complexity change?**
5. **Real problem or imagined?**

## Red Flags (Reject)

- "More flexible"
- "Cleaner"
- "Best practices"
- No concrete problem
- "Might need later"

## Green Flags (May Proceed)

- Pattern emerged 3+ times (Rule of Three)
- Clear bugs from current structure
- Fewer lines after refactor
- Deletion opportunity

## Output Format

```
REFACTOR: [Area]

Problem: [Specific, concrete]

Change: [What]

Before: [Brief]
After: [Brief]

Net: +X / -Y lines
Risk: [Assessment]
```

## Rules

- Require evidence of actual pain
- Prefer deletion over reorganization
- Small incremental changes
- Don't refactor while fixing bugs

## Council Review

Major refactors may need council review: `modules/process/council/`

## Reference

- PRESET: `AgentAssets/modules/commands/refactor/PRESET.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdennen0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
