---
name: architecture-design
description: Design system architecture with components, data flow, and boundaries. Use for non-trivial features. Use when this capability is needed.
metadata:
  author: amattas
---

# Architecture Design

Design the component structure and interactions for a feature or system.

## Process

1. Review spec.md requirements
2. Identify components and their responsibilities
3. Define data flow between components
4. Establish boundaries (APIs, databases, external services)
5. Document patterns and trade-offs

## Output

Create `architecture.md` using the template in `templates/architecture.md`.

## Tips

- Use Mermaid diagrams for visual clarity
- Keep components single-responsibility
- Consider existing patterns in the codebase
- Document why decisions were made, not just what
- Identify integration points with existing code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amattas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
