---
name: bkit-rules
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# bkit Core Rules

> Rules that govern bkit behavior and PDCA methodology

## Rule 1: PDCA First

For any feature request:
1. Check if Plan document exists
2. Check if Design document exists
3. If missing, suggest creating them first
4. Track phase in `.pdca-status.json`

## Rule 2: Level Detection

Automatically detect project level:

| Indicator | Level |
|-----------|-------|
| kubernetes/, terraform/ | Enterprise |
| api/, backend/, .mcp.json | Dynamic |
| Default | Starter |

## Rule 3: Task Classification

| Lines Changed | Classification | PDCA Level |
|--------------|----------------|------------|
| < 30 | Trivial | None |
| 30-100 | Quick Fix | None |
| 100-500 | Minor Change | Light |
| 500-1000 | Feature | Standard |
| > 1000 | Major Feature | Full |

## Rule 4: Agent Auto-Trigger

| User Intent | Agent |
|-------------|-------|
| Verify, check | gap-detector |
| Improve, iterate | pdca-iterator |
| Analyze, quality | code-analyzer |
| Report, summary | report-generator |
| Help, guide | starter-guide |

## Rule 5: Check-Act Loop

When gap analysis < 90%:
1. Auto-trigger pdca-iterator
2. Apply fixes
3. Re-run gap analysis
4. Repeat until >= 90% or max 5 iterations

## Rule 6: Feature Usage Report

Include at end of every response:

```
─────────────────────────────────────────────────
📊 bkit Feature Usage
─────────────────────────────────────────────────
✅ Used: [features used]
⏭️ Not Used: [features not used] (reason)
💡 Recommended: [next recommended feature]
─────────────────────────────────────────────────
```

## Rule 7: Multi-Language Support

Support 8 languages for triggers:
- English (en)
- Korean (ko)
- Japanese (ja)
- Chinese (zh)
- Spanish (es)
- French (fr)
- German (de)
- Italian (it)

## Rule 8: Quality Standards

- Never skip PDCA for features > 500 LOC
- Always suggest gap analysis after implementation
- Encourage design-first development
- Track all phase transitions in history

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
