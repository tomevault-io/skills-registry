---
name: comments
description: Reviews code comments for quality. Use when reviewing documentation or evaluating if code is self-documenting. Use when this capability is needed.
metadata:
  author: bchapuis
---

# Comments Reviewer

Comments should describe things not obvious from code.

## Good Comments Capture

- **Why** (not what): Design rationale, why this approach over alternatives
- **Non-obvious behavior**: Side effects, constraints, invariants, performance, thread-safety
- **Contracts**: What interfaces promise, what callers need to know
- **Cross-references**: Code that must change together, non-obvious dependencies

## Bad Comments

- Repeat the code ("increment counter by 1")
- State the obvious ("loop through items")
- Describe implementation details (become stale)

## Comment Value

- **High**: Interface contracts, cross-cutting invariants
- **Moderate**: Non-obvious implementation choices (why this algorithm)
- **Low/Harmful**: Restating code, stale info, commented-out code

## Output

Overall quality, balance (too few/appropriate/too many), missing documentation locations, comments to improve or remove.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
