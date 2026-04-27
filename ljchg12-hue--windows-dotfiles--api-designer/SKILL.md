---
name: api-designer
description: Expert API architecture including REST, GraphQL, gRPC design, versioning, and documentation Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# API Designer

## Purpose
Design robust, scalable APIs including REST, GraphQL, and gRPC with proper versioning, documentation, and evolution strategies.

## Activation Keywords
- API design, API architecture
- REST design, GraphQL schema
- gRPC, Protocol Buffers
- API versioning, deprecation
- API gateway

## Core Capabilities

### 1. REST API Design
- Resource modeling
- URL structure
- HTTP methods semantics
- Status codes
- HATEOAS

### 2. GraphQL
- Schema design
- Type system
- Query optimization
- Subscription design
- Federation

### 3. gRPC
- Service definition
- Message design
- Streaming patterns
- Error handling
- Interceptors

### 4. API Gateway
- Routing
- Rate limiting
- Authentication
- Request transformation
- Response aggregation

### 5. Documentation
- OpenAPI 3.0
- GraphQL SDL
- Protocol Buffers
- API changelog

## Design Principles

```
1. Consistency
   - Naming conventions
   - Error formats
   - Response structures

2. Evolvability
   - Versioning strategy
   - Backward compatibility
   - Deprecation policy

3. Security
   - Authentication
   - Authorization
   - Input validation

4. Performance
   - Pagination
   - Filtering
   - Field selection
   - Caching headers
```

## API Design Checklist

```markdown
## Endpoint Design
- [ ] Resources properly named (nouns, plural)
- [ ] Correct HTTP methods used
- [ ] Consistent URL structure
- [ ] Query params for filtering/sorting

## Response Design
- [ ] Consistent response envelope
- [ ] Proper status codes
- [ ] Error format standardized
- [ ] Pagination metadata included

## Security
- [ ] Authentication documented
- [ ] Authorization rules clear
- [ ] Rate limits defined
- [ ] Input validation rules

## Documentation
- [ ] All endpoints documented
- [ ] Examples for each endpoint
- [ ] Error codes documented
- [ ] Versioning strategy documented
```

## Protocol Selection Guide

| Need | Recommendation |
|------|----------------|
| Public API | REST + OpenAPI |
| Frontend flexibility | GraphQL |
| Internal microservices | gRPC |
| Real-time | WebSocket/GraphQL Subscriptions |
| High throughput | gRPC |

## Example Usage

```
User: "Design API for a booking system"

API Designer Response:
1. Resource identification
   - /bookings, /users, /properties, /availability

2. Endpoint design
   - POST /bookings (create)
   - GET /properties/{id}/availability
   - PATCH /bookings/{id}/cancel

3. GraphQL alternative schema
4. Authentication strategy (OAuth2)
5. Versioning strategy (URL-based /v1/)
6. OpenAPI specification
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
