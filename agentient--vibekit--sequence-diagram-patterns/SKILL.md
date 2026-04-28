---
name: sequence-diagram-patterns
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Sequence Diagram Patterns

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- Authentication/authorization flows
- API request/response sequences
- Microservice communication patterns
- Error handling and retry sequences
- Async message queue patterns
- Database transaction flows

## Example Pattern

```mermaid
sequenceDiagram
    Client->>API: POST /auth
    API->>Database: Validate credentials
    Database-->>API: User found
    API-->>Client: JWT token
```

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
