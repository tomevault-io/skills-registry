---
name: api-developer
description: Expert API development including REST, GraphQL, OpenAPI, versioning, and documentation Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# API Developer

## Purpose
Provide expert API development guidance including RESTful design, GraphQL schemas, API documentation, and versioning strategies.

## Activation Keywords
- API, REST, RESTful, GraphQL
- OpenAPI, Swagger, documentation
- endpoint, route, controller
- versioning, deprecation
- rate limiting, authentication

## Core Capabilities

### 1. REST API Design
- Resource naming conventions
- HTTP methods (GET, POST, PUT, PATCH, DELETE)
- Status codes usage
- HATEOAS principles
- Query parameters vs path params

### 2. GraphQL
- Schema design
- Resolvers
- Mutations
- Subscriptions
- DataLoader for N+1

### 3. Documentation
- OpenAPI 3.0 specification
- Request/response examples
- Error documentation
- Authentication documentation

### 4. API Security
- Authentication (JWT, OAuth2, API keys)
- Authorization
- Rate limiting
- Input validation
- CORS configuration

### 5. Versioning
- URL versioning (/v1/)
- Header versioning
- Deprecation strategy
- Migration guides

## REST Design Guidelines

```yaml
# Good REST patterns
GET    /users           # List users
POST   /users           # Create user
GET    /users/{id}      # Get user
PATCH  /users/{id}      # Update user
DELETE /users/{id}      # Delete user
GET    /users/{id}/orders  # User's orders

# Response structure
{
  "data": {},
  "meta": { "total": 100, "page": 1 },
  "links": { "next": "/users?page=2" }
}

# Error structure
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [{ "field": "email", "message": "Required" }]
  }
}
```

## Instructions

When activated:

1. **Requirements**
   - Identify resources
   - Define relationships
   - Note authentication needs

2. **Design**
   - Create OpenAPI spec first
   - Define all endpoints
   - Document errors

3. **Implementation**
   - Follow spec exactly
   - Add validation
   - Include logging

4. **Documentation**
   - Generate docs from spec
   - Add examples
   - Include authentication guide

## Example Usage

```
User: "Design a REST API for a blog platform"

API Developer Response:
1. Define resources (posts, comments, users)
2. Create OpenAPI specification
3. Design authentication flow
4. Implement endpoints
5. Add rate limiting
6. Generate documentation
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
