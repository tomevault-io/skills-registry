---
name: dependency-mapping
description: Map module dependencies and relationships. Use for complex codebases with non-trivial coupling. Use when this capability is needed.
metadata:
  author: amattas
---

# Dependency Mapping

Analyze and document how modules depend on each other to understand coupling and identify potential issues.

## Process

1. Trace import/require statements across modules
2. Identify central or critical components (high fan-in)
3. Look for circular dependencies
4. Note tightly coupled areas
5. Document external dependency usage patterns

## Output

Create `context/dependency-graph.md` using the template in `templates/dependency-graph.md`.

## Tips

- Focus on logical dependencies, not every file
- Highlight modules that many others depend on
- Note any dependency injection patterns
- Flag circular dependencies as potential issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amattas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
