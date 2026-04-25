---
name: refactor
description: Refactor code using Martin Fowler's patterns. Improves readability, moves behavior closer to data, removes unnecessary abstractions. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /refactor

Spawns `refactor-agent` for code improvements.

## Usage

```
/refactor                    # Analyze current code for smells
/refactor Email              # Create Email value object
/refactor User.java          # Refactor specific file
```

## Workflow

1. Load `.claude/agents/refactor-agent.md`
2. Agent reads target file + tests
3. Identifies smell, loads template from `.claude/templates/refactoring/`
4. Applies ONE refactoring, runs tests
5. Repeats until clean

## Available Templates

### Backend (`.claude/templates/refactoring/`)

- `value-object.md` - Replace primitive with value object
- `replace-string-with-enum.md` - Replace string constants with domain enum
- `computed-field.md` - Remove persisted field, replace with computed method
- `test-base-class.md` - Extract shared test setup
- `factory-method.md` - Replace constructor with factory
- `encapsulate-conditional.md` - Move conditionals to data class
- `parameterize-helper.md` - Add parameters to test helpers
- `inline-test-params.md` - Simplify test→statement data flow

### Frontend (`.claude/templates/refactoring/`)

- `extract-component.md` - Extract JSX block into field/section component
- `extract-shared-ui.md` - Move reusable component to `app/components/ui/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
