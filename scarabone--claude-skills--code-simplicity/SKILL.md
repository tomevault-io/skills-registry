---
name: code-simplicity
description: Favors simple, maintainable solutions over complex ones. Use when providing code, automations, or technical implementations. Use when this capability is needed.
metadata:
  author: scarabone
---

# Code Simplicity Enforcer

## Core Philosophy

Brett's stated preference: "simple, reliable automations"

This means:
- Simple beats clever
- Reliable beats cutting-edge
- Documented beats experimental
- Fewer dependencies beats feature-rich
- Maintainable beats optimized
- Readable beats compact

## Decision Framework

Before suggesting ANY implementation, ask:

### 1. Is there a simpler way?
- Native feature vs custom code?
- Built-in integration vs HACS?
- UI configuration vs YAML?
- One tool vs orchestrating multiple?

### 2. Will this be maintainable in 6 months?
- Can future-you understand it?
- Will it break with updates?
- Does it require ongoing attention?
- Is documentation clear?

### 3. What's the dependency cost?
- How many things need to stay working?
- What breaks if one piece fails?
- Can you upgrade independently?

### 4. What's the complexity justified by?
- Significant capability gain?
- Unique requirement that simple can't meet?
- Future-proofing that's actually needed?

## Simplicity Hierarchy

### Tier 1: Prefer Native Solutions
**Example:** Home Assistant built-in automation
- Lowest maintenance
- Best compatibility
- Most reliable
- Easiest troubleshooting

### Tier 2: Well-Established Tools
**Example:** Popular HACS integration with active maintenance
- Proven in production
- Large user base
- Active development
- Good documentation

### Tier 3: Custom Code (Last Resort)
**Example:** Custom Python script or complex automation
- Use only when Tier 1 & 2 can't solve it
- Requires ongoing maintenance
- You own the bugs
- Document extensively

## Red Flags: Overly Complex Solutions

Watch out for:
- "Just write a quick script that..."
- Multiple service calls in sequence
- Complex data transformations
- Fragile timing dependencies
- Heavy state management
- Nested conditionals >3 deep
- Template sensors that need templates
- Custom components for simple tasks

## Communication Pattern

### If Suggesting Complex Solution:
1. Acknowledge the complexity
2. Explain WHY simple won't work
3. Show what you've ruled out
4. Provide escape hatches (how to simplify later)

### If User Requests Complex Solution:
1. Understand the actual goal
2. Suggest simpler alternatives
3. If they insist, implement but warn

## The "Weekend Test"

Would you be comfortable troubleshooting this at 10 PM on a Friday?

If no → too complex

## Summary

Default to simple. Only add complexity when:
1. Simple truly won't work (prove it)
2. The benefit justifies the cost (be specific)
3. You're willing to maintain it (be honest)

When in doubt, pick the solution that's easier to understand, easier to debug, and easier to replace.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
