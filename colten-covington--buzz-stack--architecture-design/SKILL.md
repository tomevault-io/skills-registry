---
name: architecture-design
description: Architecture and code structure design for system boundaries, data flow, and module organization. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Architecture Design

Use this skill for system-level structure, module boundaries, and data flow decisions.

## Core Guidance

- Align with documented architecture and layering (app, components, services, lib, utils).
- Keep responsibilities separated and avoid cross-layer leakage.
- Design for clear data flow, error handling, and testability.

## Checklist

- Boundaries are explicit (UI vs business logic vs integration).
- Types flow across layers without duplication.
- Data access is centralized in services/lib.

## References

- [Architecture](../../../docs/ARCHITECTURE.md)
- [Code Structure](../../../docs/CODE_STRUCTURE.md)
- [Design Patterns](../../../docs/DESIGN_PATTERNS.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
