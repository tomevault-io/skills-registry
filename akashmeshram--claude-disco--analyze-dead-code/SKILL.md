---
name: analyze-dead-code
description: Find unused code, deprecated paths, and documentation drift. Use when cleaning up codebase, preparing for refactor, or auditing technical debt. Use when this capability is needed.
metadata:
  author: akashmeshram
---

# Analyze Dead Code

Detect unused and deprecated code using the `dead-code-analyzer` agent.

## What It Finds

1. **Dead Code** - Functions, classes, modules, variables never used
2. **Deprecated Paths** - Code reachable but abandoned or marked for removal
3. **Documentation Drift** - Code that contradicts README, comments, or docstrings

## When to Use

- "Clean up unused code"
- "Is this function used anywhere?"
- "Audit before major refactor"
- "Check if docs match implementation"

## Output

```
### Dead Code Inventory
| Location | Type | Confidence | Notes |
|----------|------|------------|-------|

### Deprecated Paths
| Location | Pattern | Signal | Recommendation |

### Documentation Divergence
| Documented Claim | Actual State | Severity |

### Summary
- High confidence (safe to remove): X items
- Medium confidence (verify first): X items
- Priority actions: [list]
```

## Agent

Use `subagent_type: dead-code-analyzer`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akashmeshram) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
