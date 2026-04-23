---
name: api-design
description: REST and GraphQL API design best practices including HTTP methods, status codes, and documentation. Use when this capability is needed.
metadata:
  author: kprsnt2
---

# API Design Best Practices

## REST API Design
- Use nouns for resources (/users, /orders)
- Use HTTP methods correctly (GET, POST, PUT, DELETE)
- Use proper status codes
- Version your API (/v1/users)
- Use plural nouns

## HTTP Status Codes
- 200 OK - Success
- 201 Created - Resource created
- 204 No Content - Deletion success
- 400 Bad Request - Client error
- 401 Unauthorized - Authentication required
- 403 Forbidden - Not allowed
- 404 Not Found - Resource not found
- 500 Internal Server Error - Server error

## Request/Response
- Use JSON consistently
- Use camelCase for field names
- Include error details in responses
- Implement pagination for lists
- Use HATEOAS for discoverability

## GraphQL
- Define clear schema
- Use fragments for reuse
- Implement proper error handling
- Use DataLoader for N+1
- Implement query complexity limits

## Documentation
- Use OpenAPI/Swagger for REST
- Document all endpoints
- Include request/response examples
- Document error responses
- Keep docs in sync with code

## Versioning
- Use URL versioning (/v1/)
- Or use header versioning
- Maintain backward compatibility
- Deprecate gracefully

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kprsnt2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
