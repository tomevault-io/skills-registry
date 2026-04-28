---
name: c4-model-architecture-diagrams
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# C4 Model Architecture Diagrams

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

**C4 Model Hierarchy**:

```mermaid
graph TD
    A[C1: System Context] -->|Zoom in| B[C2: Container]
    B -->|Zoom in| C[C3: Component]
    C -->|Zoom in| D[C4: Code]
```

- **C1 System Context**: High-level system boundaries and external actors
- **C2 Container**: Applications, data stores, microservices
- **C3 Component**: Internal structure of containers
- **C4 Code**: Class/module level detail (optional)
- Structurizr DSL integration
- PlantUML C4 extension support

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
