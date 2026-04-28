---
name: access-control-patterns
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Access Control Patterns

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- **IDOR Detection**: Identify Insecure Direct Object Reference vulnerabilities
- **RBAC Patterns**: Role-Based Access Control implementation guidance
- **ABAC Patterns**: Attribute-Based Access Control strategies
- **Privilege Escalation Prevention**: Detect and prevent unauthorized privilege elevation
- Ownership verification patterns
- Resource authorization best practices

## Critical Pattern

```typescript
// WRONG - no ownership check
const post = await db.posts.findById(params.id);

// CORRECT - verify ownership
const post = await db.posts.findById(params.id);
if (post.authorId !== session.userId) {
  throw new ForbiddenError();
}
```

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
