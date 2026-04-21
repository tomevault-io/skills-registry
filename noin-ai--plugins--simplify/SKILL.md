---
name: simplify
description: Show proposed changes without applying Use when this capability is needed.
metadata:
  author: noin-ai
---

# Code Simplification Skill

Simplify code using `noin-ai:code-simplifier` agent.

## Usage

```bash
/simplify                    # Recently modified code
/simplify <file>             # Specific file
/simplify --aggressive       # Significant refactoring
/simplify --dry-run          # Preview only
```

## What It Does

- Remove unnecessary complexity
- Flatten nested conditions
- Extract meaningful names
- Eliminate dead code
- Simplify control flow

## Workflow

```
/simplify [target]
    ↓
Resolve target (file/dir/recent)
    ↓
Task(noin-ai:code-simplifier, target, options)
    ↓
Return simplified code or diff
```

## Examples

```bash
/simplify src/utils/parser.ts
/simplify src/components/ --aggressive
/simplify --dry-run
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noin-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
