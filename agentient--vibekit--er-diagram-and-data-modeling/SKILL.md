---
name: er-diagram-and-data-modeling
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# ER Diagram and Data Modeling

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- Entity-relationship diagram generation
- Cardinality and relationship notation
- Primary/foreign key identification
- Normalization analysis (1NF, 2NF, 3NF)
- Index recommendation
- Schema migration planning

## Mermaid ER Syntax

```mermaid
erDiagram
    USER ||--o{ POST : creates
    USER {
        uuid id PK
        string email
        timestamp created_at
    }
    POST {
        uuid id PK
        uuid author_id FK
        string title
        text content
    }
```

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
