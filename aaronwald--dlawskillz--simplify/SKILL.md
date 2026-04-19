---
name: simplify
description: Analyze code for simplification opportunities. Use when user says "simplify", "reduce complexity", "clean up code", "remove duplication", "too complicated", or wants to reduce technical debt. Use when this capability is needed.
metadata:
  author: aaronwald
---
# simplify

Find and apply simplification opportunities in the codebase or a specific area.

## Instructions

1. Identify the scope - specific files, a module, or a broader area
2. Analyze the code looking for:
   - **Duplicate code** that can be extracted into shared functions
   - **Dead code** that is never called or referenced
   - **Over-abstraction** where indirection adds complexity without value
   - **Unnecessary wrappers** around simple operations
   - **Complex conditionals** that can be flattened or simplified
   - **Unused dependencies** or imports
3. Prioritize by impact: prefer changes that remove the most code with the least risk
4. Present findings to the user before making changes
5. Apply changes incrementally - one simplification at a time
6. Run `superpowers:requesting-code-review` after changes to verify quality

## Principles

- Three similar lines of code is better than a premature abstraction
- Only simplify what exists - don't redesign for hypothetical futures
- Removing code is the best simplification
- If a "simplification" makes the code harder to understand, it's not simpler

## Examples

### Simplify a specific module
```
User: /simplify the secmaster sync code
Claude: Analyzing ssmd/internal/secmaster/...
Found 3 opportunities:
1. bulkUpsertEvents and bulkUpsertMarkets share 80% identical batching logic - extract shared batchInsert helper
2. Dead code: unused formatMarketName function (removed in refactor but never deleted)
3. Three separate error-wrapping patterns - standardize on one
```

### Broad codebase scan
```
User: /simplify
Claude: What area should I focus on? (specific files, a module, or the whole project?)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aaronwald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
