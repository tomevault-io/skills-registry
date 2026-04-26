---
name: meta-skill-routing
description: Strategies for dynamic skill discovery to minimize context overhead. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Meta-Skill Routing

When a library has dozens of skills, you cannot load them all. You must "route" your attention effectively.

## The Two-Step Pattern
1.  **Index Lookup**: Read the root `README.md` or a dedicated index to identify which skills are relevant to the current request.
2.  **Explicit Load**: Only load the content of the specific `SKILL.md` files needed.

## Routing Heuristics
- **Keyword Matching**: Look for domain-specific keywords (e.g., "Flask", "TDD", "Transformers").
- **Intent Discovery**: Ask, "Is this a planning, implementing, or verifying task?" and route to the corresponding skill suite.

## Best Practices
- **Atomic Loading**: Avoid "just in case" loading. Only load a skill when you are about to perform an action related to it.
- **Reference Over Inclusion**: Link to skills in documentation instead of copying their content.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
