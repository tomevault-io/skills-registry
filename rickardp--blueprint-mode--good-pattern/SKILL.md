---
name: blueprintgood-pattern
description: Capture good code as a reusable example Use when this capability is needed.
metadata:
  author: rickardp
---

# Capture Good Pattern

**COMMAND:** Extract code as a pattern others should follow.

## Execute

1. **Parse** argument for file path or description
2. **Find** file (search if only description given)
3. **Read** file content
4. **Create** pattern at patterns/good/[name].[ext]
5. **Report** what was captured

## Input Handling

| Input | Action |
|-------|--------|
| `/good-pattern src/repos/user.ts` | Read file, capture as pattern |
| `/good-pattern error handling in api` | Search for files, ask which one |
| `/good-pattern` | Ask for file path |

## Pattern Template

Use template from `_templates/TEMPLATES.md` (`<!-- SECTION: good-patterns -->`).

```[language]
/**
 * [Pattern Name]
 *
 * USE THIS PATTERN WHEN:
 * - [Situation where this applies]
 *
 * KEY ELEMENTS:
 * 1. [Important aspect]
 *
 * Related ADRs:
 * - [ADR-NNN](../../docs/adrs/NNN-name.md) - [Why this pattern]
 *
 * Source: [original file path]
 */

// --- Example Implementation ---

// ADR-NNN: [Brief reference to the decision]
[extracted code]
```

## Output

```
Pattern captured at patterns/good/[name].[ext]
```

## Directory Structure

Create `patterns/good/` if it doesn't exist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rickardp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
