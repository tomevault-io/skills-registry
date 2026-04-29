---
name: api-architecture
description: Target API paradigm Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# API Architecture Skill

## Purpose
Select and design the optimal API architecture for your use case.

## Decision Matrix

```
┌──────────────────────────────────────────────────────────────────┐
│                    API Style Selection                            │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  Use Case                              → Recommended Style        │
│  ─────────────────────────────────────────────────────────────   │
│  Public API + Wide adoption            → REST (OpenAPI 3.1)      │
│  Complex queries + Frontend-heavy      → GraphQL                 │
│  High performance + Internal           → gRPC                    │
│  Mobile + Offline support              → REST + GraphQL hybrid   │
│  Microservices communication           → gRPC / Event-driven     │
│  Real-time updates                     → GraphQL Subscriptions   │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

## API Comparison

| Aspect | REST | GraphQL | gRPC |
|--------|------|---------|------|
| **Learning curve** | Low | Medium | High |
| **Flexibility** | Low | High | Medium |
| **Performance** | Good | Good | Excellent |
| **Caching** | Easy (HTTP) | Complex | Custom |
| **Tooling** | Excellent | Good | Good |
| **Documentation** | OpenAPI | SDL | Protobuf |
| **Browser support** | Native | Native | Limited |

## REST Architecture (Richardson Maturity Model)

```
Level 0: Single endpoint, POST everything
Level 1: Multiple endpoints per resource
Level 2: Proper HTTP methods + status codes ← Target
Level 3: HATEOAS (hypermedia links)
```

### Resource Design

```yaml
# Good resource naming
GET /api/v1/users              # List users
GET /api/v1/users/{id}         # Get user
POST /api/v1/users             # Create user
PUT /api/v1/users/{id}         # Replace user
PATCH /api/v1/users/{id}       # Update user
DELETE /api/v1/users/{id}      # Delete user

# Nested resources (max 2 levels)
GET /api/v1/users/{id}/orders  # User's orders

# Actions (when CRUD doesn't fit)
POST /api/v1/orders/{id}/cancel
POST /api/v1/users/{id}/verify
```

## GraphQL Architecture

```graphql
# Schema-first design
type Query {
  user(id: ID!): User
  users(first: Int, after: String): UserConnection!
}

type Mutation {
  createUser(input: CreateUserInput!): CreateUserPayload!
  updateUser(id: ID!, input: UpdateUserInput!): UpdateUserPayload!
}

type Subscription {
  userCreated: User!
  orderStatusChanged(orderId: ID!): Order!
}
```

## Hybrid Strategy

```
┌─────────────────────────────────────────────────────────┐
│                    API Gateway                           │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Public Clients ────► REST API (OpenAPI)                │
│                                                          │
│  Web/Mobile App ────► GraphQL API                       │
│                                                          │
│  Internal Services ─► gRPC                              │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Versioning Strategies

| Strategy | Example | Pros | Cons |
|----------|---------|------|------|
| URL path | `/api/v1/users` | Clear, cacheable | URL pollution |
| Header | `Accept: application/vnd.api.v1+json` | Clean URLs | Hidden |
| Query param | `/api/users?version=1` | Flexible | Unconventional |

**Recommendation:** URL versioning for public APIs, header for internal.

---

## Unit Test Template

```typescript
import { describe, it, expect } from 'vitest';
import { selectApiStyle, validateResourceNaming } from './api-architecture';

describe('API Architecture Skill', () => {
  describe('selectApiStyle', () => {
    it('should recommend REST for public APIs', () => {
      const result = selectApiStyle({
        use_case: 'public_api',
        audience: 'external',
      });
      expect(result.style).toBe('rest');
      expect(result.reasons).toContain('wide tooling support');
    });

    it('should recommend GraphQL for complex frontend needs', () => {
      const result = selectApiStyle({
        use_case: 'internal_api',
        complex_queries: true,
        frontend_heavy: true,
      });
      expect(result.style).toBe('graphql');
    });
  });

  describe('validateResourceNaming', () => {
    it('should accept valid resource names', () => {
      expect(validateResourceNaming('/api/v1/users')).toBe(true);
      expect(validateResourceNaming('/api/v1/users/{id}/orders')).toBe(true);
    });

    it('should reject invalid resource names', () => {
      expect(validateResourceNaming('/api/v1/getUsers')).toBe(false); // verb in name
      expect(validateResourceNaming('/api/v1/user')).toBe(false); // singular
    });
  });
});
```

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Over-fetching (REST) | Too much data returned | Add field selection or use GraphQL |
| Under-fetching (REST) | Multiple round trips | Embed related resources or use GraphQL |
| N+1 queries (GraphQL) | Resolver per field | Use DataLoader for batching |
| Version conflicts | Breaking changes | Semantic versioning + deprecation policy |

---

## Quality Checklist

- [ ] API style matches use case requirements
- [ ] Resource naming follows conventions
- [ ] Versioning strategy defined
- [ ] Error response format standardized
- [ ] Pagination strategy chosen
- [ ] Rate limiting planned

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
