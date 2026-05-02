---
name: asking-users
description: AskUserQuestion protocol for confidence gates. Use when uncertain or before destructive actions. Use when this capability is needed.
metadata:
  author: codetonight-sa
---

# Asking Users

AskUserQuestion is MANDATORY when confidence drops below threshold.

## When to Use (MANDATORY)

| Trigger | Action |
|---------|--------|
| Confidence < 99% | HALT and ask |
| Destructive action | HALT and confirm |
| UI/UX decision | HALT and present options |
| Multi-step task | Checkpoint every 3 steps |
| Assumption identified | HALT and validate |

## The Bidirectional Pattern

Every question teaches AND collects:

```
Question → User learns something → We collect preference
```

### Good Example

```
Question: "Icon placement affects perceived hierarchy. Where should the brand icon appear?"
Options:
- "Header (most prominent)" - User learns placement = prominence
- "Footer (subtle branding)" - User learns alternative

Teaching: User learns about visual hierarchy
Collecting: Brand prominence preference
```

## Question Structure

```
Question: Clear, specific question ending with ?
Header: Short label (max 12 chars)
Options:
- "Option 1" - Description of what this means
- "Option 2" - Description of what this means
- "Option 3" - Description of what this means
```

## Rules

1. **2-4 options only** - Never more than 4
2. **No "Other" in options** - System adds it automatically
3. **Descriptions explain implications** - Not just what, but why
4. **Headers are short** - 12 characters max

## Anti-Patterns

| Don't | Do |
|-------|-----|
| "Is this okay?" | Specific question about the choice |
| 5+ options | 2-4 focused options |
| "Other" as option | Let system add it |
| Vague headers | "Database", "Auth", "Layout" |

## Token Budget

| Component | Tokens |
|-----------|--------|
| Skill load | ~400 |
| Per question | ~150 |

## Foundation Status

This skill is ALWAYS ACTIVE. It cannot be disabled by any mode.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codetonight-sa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
