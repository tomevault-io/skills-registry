---
name: clean-code
description: description: Pragmatic coding standards - concise, direct, no over-engineering Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: clean-code
description: Pragmatic coding standards - concise, direct, no over-engineering
priority: CRITICAL
---

# Clean Code Standards

> Be **concise, direct, and solution-focused**.

---

## Core Principles

| Principle | Rule |
|-----------|------|
| SRP | Single Responsibility - one thing per function/class |
| DRY | Don't Repeat Yourself |
| KISS | Keep It Simple |
| YAGNI | You Aren't Gonna Need It |

---

## Naming

| Element | Convention | Example |
|---------|------------|---------|
| Variables | Reveal intent | `userCount` not `n` |
| Functions | Verb + noun | `getUserById()` |
| Booleans | Question form | `isActive`, `hasPermission` |
| Constants | SCREAMING_SNAKE | `MAX_RETRY_COUNT` |

---

## Functions

| Rule | Guideline |
|------|-----------|
| Small | Max 20 lines |
| One Thing | Single purpose |
| Few Args | Max 3 parameters |
| No Side Effects | Don't mutate inputs |

---

## Code Structure

| Pattern | Apply |
|---------|-------|
| Guard Clauses | Early returns |
| Flat > Nested | Max 2 levels |
| Composition | Small functions together |

---

## Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Comment every line | Self-documenting names |
| Helper for one-liner | Inline the code |
| Factory for 2 objects | Direct instantiation |
| Deep nesting | Guard clauses |
| Magic numbers | Named constants |

---

## Before Editing

| Question | Why |
|----------|-----|
| What imports this file? | They might break |
| What tests cover this? | Tests might fail |
| Is this shared? | Multiple places affected |

---

## Self-Check

| Check | Question |
|-------|----------|
| ✅ Goal met? | Did exactly what was asked? |
| ✅ Code works? | Verified the change? |
| ✅ No errors? | Lint and types pass? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
