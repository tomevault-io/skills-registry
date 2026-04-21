---
name: api-design
description: Reviews API designs for simplicity and error handling. Use when designing APIs, evaluating interface complexity, or eliminating error conditions. Use when this capability is needed.
metadata:
  author: bchapuis
---

# API Design Reviewer

## Principles

1. **Define Errors Out of Existence**: Make invalid states unrepresentable, handle edge cases internally, provide sensible defaults
2. **Pull Complexity Downward**: Common cases trivially easy, callers don't need implementation details
3. **Minimize Interface**: Fewer methods > more, fewer parameters > more, consistent patterns
4. **Appropriate Generality**: Somewhat general-purpose, but don't over-engineer

## Checklist

- Public method count? Parameters per method?
- Required init/cleanup steps? Conjoined methods?
- What errors can callers encounter? Which could be eliminated by design?
- Hidden requirements or gotchas?
- Can implementation change without affecting callers?

## Common Smells

- Too many parameters → use config objects or builders
- Required init/cleanup → use automatic resource management
- Error-prone sequences → handle order internally
- Unnecessary errors → return optionals/defaults instead of throwing

## Output

API overview, strengths, issues (problem, principle, current vs suggested, impact), prioritized recommendations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
