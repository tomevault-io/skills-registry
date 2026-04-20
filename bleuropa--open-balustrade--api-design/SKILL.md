---
name: api-design
description: RESTful API design patterns and best practices for consistent, maintainable endpoints Use when this capability is needed.
metadata:
  author: bleuropa
---

# API Design Skill

Best practices for designing clear, consistent, and maintainable REST APIs.

## Core Principles

1. **Consistency** - Follow same patterns across all endpoints
2. **Predictability** - Behavior matches expectations
3. **Simplicity** - Easy to understand and use
4. **Versioning** - Plan for changes over time
5. **Documentation** - Clear, up-to-date docs

## Resource Naming

### Use Nouns, Not Verbs

```
Bad:  /getUsers, /createUser, /deleteUser
Good: GET /users, POST /users, DELETE /users/:id
```

### Plural Resource Names

```
Bad:  /user/:id
Good: /users/:id
```

### Nested Resources (max 2 levels)

```
/users/:userId/posts
/posts/:postId/comments
```

## HTTP Methods

```
GET    /users          # List all users
GET    /users/:id      # Get specific user
POST   /users          # Create new user
PUT    /users/:id      # Replace entire user
PATCH  /users/:id      # Update partial user
DELETE /users/:id      # Delete user
```

## Status Codes

### Success (2xx)

- 200 OK - Successful GET, PUT, PATCH
- 201 Created - Successful POST
- 204 No Content - Successful DELETE

### Client Errors (4xx)

- 400 Bad Request - Invalid input
- 401 Unauthorized - Not authenticated
- 403 Forbidden - Not allowed
- 404 Not Found - Resource doesn't exist
- 422 Unprocessable - Validation failed
- 429 Too Many Requests - Rate limited

### Server Errors (5xx)

- 500 Internal Error - Server error
- 503 Service Unavailable - Overloaded

## Request/Response Format

### Success Response

```json
{
  "data": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

### List Response with Pagination

```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 100,
    "totalPages": 5
  }
}
```

### Error Response

```json
{
  "error": {
    "code": "INVALID_INPUT",
    "message": "Email is required",
    "details": {
      "field": "email",
      "reason": "missing_field"
    }
  }
}
```

## Query Parameters

### Filtering

```
GET /users?role=admin&active=true
```

### Sorting

```
GET /users?sort=name
GET /users?sort=-created_at  # descending
```

### Pagination

```
GET /users?page=2&per_page=20
```

### Field Selection

```
GET /users?fields=id,name,email
```

## Versioning

### URL Versioning (Recommended)

```
/api/v1/users
/api/v2/users
```

## Authentication

### Bearer Token

```
Authorization: Bearer <token>
```

## Rate Limiting Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640000000
```

## Best Practices Checklist

- [ ] Use nouns for resources
- [ ] Plural resource names
- [ ] Proper HTTP methods
- [ ] Appropriate status codes
- [ ] Consistent JSON structure
- [ ] Input validation
- [ ] Error handling
- [ ] Authentication
- [ ] Rate limiting
- [ ] Pagination for lists
- [ ] API versioning
- [ ] Documentation
- [ ] HTTPS only

## Remember

- Consistency is key
- Think from client perspective
- Design for evolution
- Document everything
- Security first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bleuropa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
