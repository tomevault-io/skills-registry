---
name: applying-linus-standards
description: | Use when this capability is needed.
metadata:
  author: 15195999826
---

# Applying Linus Standards

## Pre-Check (Before Any Work)

1. **Real problem?** - If imaginary, reject
2. **Simpler way?** - If yes, use it
3. **Break anything?** - Dev: fix now. Prod: weigh cost

## Analysis Framework

When analyzing requirements or designs, consider these layers:

| Layer | Key Question |
|-------|-------------|
| Data Structure | What's the core data? Can it be simpler? |
| Special Cases | Can if/else branches be eliminated by better data design? |
| Complexity | Can concepts be reduced by half? |
| Practicality | Does the problem actually exist in production? |

## Output Guidelines

For requirements/design decisions, include:
- **Judgment**: Worth doing or not, with reason
- **Key insight**: Data structure issues, eliminable complexity, risks
- **Approach**: Prioritize data simplification over code cleverness

For code review, focus on:
- Overall taste assessment
- Fatal issues (if any)
- Concrete improvement suggestions ("eliminate this branch", "10 lines → 3")

Adapt format based on context. These are guidelines, not rigid templates.

## Key Principles

- **Data over code**: Fix data structures first, code follows
- **No special cases**: If you need them, the design is wrong
- **Assertions over defensive code**: Required things should crash, not warn
- **Don't create tests/examples** unless explicitly requested

---
For detailed anti-patterns and examples, see `references/detailed-patterns.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/15195999826) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
