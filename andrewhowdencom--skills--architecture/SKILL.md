---
name: architecture
description: Guidelines for application structure and dependency management. Use when this capability is needed.
metadata:
  author: andrewhowdencom
---

# Architecture

## Hexagonal Architecture
For complex applications (clients, datastore, state), use [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture).

## Dependency Management
- **Initializers**: Create dependencies with sane defaults.
- **Optional Arguments**: Use the **Functional Options Pattern** (variadic args) for optional configuration.

See [Languages](../languages/SKILL.md) for implementation details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewhowdencom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
