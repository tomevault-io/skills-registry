---
name: flutter-feature-based-clean-architecture
description: Standards for organizing code by feature at the root level to improve scalability and maintainability. Use when this capability is needed.
metadata:
  author: fierzone
---

# Feature-Based Clean Architecture

## **Priority: P0 (CRITICAL)**

Standard for modular Clean Architecture organized by business features in `lib/features/`.

## Structure

See [references/folder-structure.md](references/folder-structure.md) for the complete directory blueprint.

## Implementation Guidelines

- **Feature Encapsulation**: Keep logic, models, and UI internal to the feature directory.
- **Strict Layering**: Maintain 3-layer separation (Domain/Data/Presentation) within each feature.
- **Dependency Rule**: `Presentation -> Domain <- Data`. Domain must have zero external dependencies.
- **Cross-Feature Communication**: Features only depend on the **Domain** layer of other features.
- **Flat features**: Keep `lib/features/` flat; avoid nested features.
- **No DTO Leakage**: Never expose DTOs or Data Sources to UI or other features; return Domain Entities.
- **Shared logic**: Move cross-cutting concerns to `lib/shared/` or `lib/core/`.

## Reference & Examples

For feature folder blueprints and cross-layer dependency templates:
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

layer-based-clean-architecture | retrofit-networking | go-router-navigation | bloc-state-management | dependency-injection

---
> Source: [fierzone/agent-skills-standard](https://github.com/fierzone/agent-skills-standard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
