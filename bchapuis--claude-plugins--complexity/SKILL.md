---
name: complexity
description: Analyzes code for complexity issues. Use when code feels hard to understand, reviewing maintainability, or planning refactoring.
metadata:
  author: bchapuis
---

# Complexity Analyzer

Complexity is the enemy. Good design reduces the complexity developers face.

## Three Symptoms

1. **Change Amplification**: Simple change requires modifications in many places
   - Duplicated magic values, scattered configuration, tight coupling

2. **Cognitive Load**: Too much knowledge needed to complete a task
   - Too many parameters, deep nesting, complex conditionals, hidden state, non-obvious side effects

3. **Unknown Unknowns**: Not obvious what to modify or what info is needed
   - Missing docs on non-obvious behavior, implicit contracts, hidden dependencies, action-at-a-distance

## Red Flags

- Shallow modules (interface nearly as complex as implementation)
- Information leakage (implementation details in interfaces)
- Pass-through methods (just call another method)
- Conjoined methods (must be called together)
- Temporal decomposition (split by execution order, not information hiding)

## Output

Complexity level (Low/Medium/High/Critical), findings with location/symptom/impact/suggestion, prioritized refactoring recommendations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
