---
name: rest-api
description: Auto-apply when designing or implementing REST APIs. Trigger this skill when the user asks to create, modify, or debug REST endpoints, API contracts, HTTP conventions, or RESTful resource modeling. Use when this capability is needed.
metadata:
  author: plutowang
---

# REST API Design Expert

You are an expert in **REST API Design**. You strictly adhere to resource-oriented architecture and HTTP semantics.

## 1. Resource Naming Conventions

### URL Structure

- **Plural nouns** for collections: `/users`, `/posts`, `/comments`
- **Nested resources** for relationships: `/users/{userId}/posts`
- **kebab-case** for multi-word paths: `/user-profiles`, `/order-items`
- **No verbs in URLs**: Use HTTP methods, not `/getUser` or `/createPost`

### Examples

```http
GET    /users              # List users
POST   /users              # Create user
GET    /users/{id}         # Get user
PATCH  /users/{id}          # Update user (partial)
PUT    /users/{id}          # Replace user (full)
DELETE /users/{id}          # Delete user

GET    /users/{userId}/posts    # Get user's posts
POST   /users/{userId}/posts    # Create post for user
```

### Anti-Patterns

```http
# Bad: verbs in URLs
GET /getUsers
POST /createUser
POST /deleteUser

# Bad: nouns as verbs
GET /users/list
POST /users/add

# Bad: inconsistent casing
GET /user_profiles  (should be /user-profiles)
```

## 2. HTTP Methods

| Method    | Semantics          | Idempotent | Safe |
| --------- | ------------------ | ---------- | ---- |
| `GET`     | Read resource      | Yes        | Yes  |
| `POST`    | Create resource    | No         | No   |
| `PUT`     | Replace resource   | Yes        | No   |
| `PATCH`   | Partial update     | No         | No   |
| `DELETE`  | Remove resource    | Yes        | No   |
| `HEAD`    | Headers only       | Yes        | Yes  |
| `OPTIONS` | Capabilities       | Yes        | Yes  |

## 3. Standard Error Format

Use a consistent error response format across all endpoints:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format",
        "code": "INVALID_FORMAT"
      },
      {
        "field": "age",
        "message": "Must be a positive integer",
        "code": "INVALID_TYPE"
      }
    ],
    "traceId": "abc123-def456"
  }
}
```

### Standard Error Codes

| HTTP Status | Code                  | Use Case                              |
| ----------- | --------------------- | ------------------------------------- |
| 400         | `BAD_REQUEST`         | Malformed request                     |
| 400         | `VALIDATION_ERROR`    | Invalid input                         |
| 401         | `UNAUTHORIZED`        | Missing or invalid authentication     |
| 403         | `FORBIDDEN`           | Authenticated but not permitted       |
| 404         | `NOT_FOUND`           | Resource doesn't exist                |
| 409         | `CONFLICT`            | Resource conflict (duplicate, etc.)   |
| 422         | `UNPROCESSABLE`       | Semantically invalid input            |
| 429         | `RATE_LIMITED`        | Too many requests                     |
| 500         | `INTERNAL_ERROR`      | Unexpected server error               |
| 503         | `SERVICE_UNAVAILABLE` | Temporarily unavailable               |

### HTTP Status Code Guidelines

- `200 OK` — Successful GET, PATCH, PUT, DELETE
- `201 Created` — Successful POST creating a resource
- `202 Accepted` — Async operation accepted
- `204 No Content` — Successful DELETE with no body
- `400 Bad Request` — Client error (validation, malformed)
- `401 Unauthorized` — Authentication required
- `403 Forbidden` — Authenticated but not authorized
- `404 Not Found` — Resource doesn't exist
- `409 Conflict` — Resource state conflict
- `422 Unprocessable Entity` — Valid syntax but semantic errors
- `429 Too Many Requests` — Rate limit exceeded
- `500 Internal Server Error` — Server-side error

## 4. Pagination

Use **cursor-based pagination** for large datasets. Never use offset pagination for large tables.

### Cursor Pagination

```json
{
  "data": [...],
  "pagination": {
    "nextCursor": "eyJpZCI6MTAwfQ==",
    "prevCursor": null,
    "hasNext": true,
    "hasPrev": false,
    "totalCount": 1500
  }
}
```

### Request Parameters

```bash
GET /users?first=20&after=eyJpZCI6MTAwfQ==
GET /posts?last=10&before=eyJpZCI6MjAwfQ==
```

### Query Parameters

| Parameter | Purpose                                             |
| --------- | --------------------------------------------------- |
| `first`  | Number of items to return (forward)                 |
| `after`  | Cursor for forward pagination                       |
| `last`   | Number of items to return (backward)                |
| `before` | Cursor for backward pagination                      |
| `filter` | JSON-encoded filter object                          |
| `sort`   | Field and direction (e.g., `createdAt:desc`)      |

## 5. Idempotency

For critical POST operations, support idempotency keys:

### Request Headers

```bash
Idempotency-Key: <unique-client-generated-key>
```

### Idempotency Response

```http
HTTP/1.1 201 Created
Idempotency-Key: abc123
```

### Implementation Notes

- Store idempotency keys with TTL (24 hours recommended)
- Return cached response for duplicate keys
- Keys should be UUIDs or similar high-entropy values

## 6. API Versioning

### URL Path Versioning (Recommended)

```bash
/api/v1/users
/api/v2/users
```

### Version Lifecycle

| Status       | Description                                           |
| ------------ | ----------------------------------------------------- |
| `current`    | Latest stable version                                 |
| `deprecated` | Still supported, migration recommended               |
| `sunset`     | Security patches only, migration required             |
| `closed`     | No longer available                                   |

### Deprecation Headers

```http
Deprecation: true
Sunset: Sat, 31 Dec 2025 23:59:59 GMT
Link: <https://api.example.com/v2/users>; rel="successor-version"
```

## 7. Input Validation

### Validation Rules

- Validate at **boundary** (entry point), not in business logic
- Reject early with clear, actionable error messages
- Use schema validation (Zod, JSON Schema, etc.)

### Example Validation Error

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address",
        "code": "INVALID_FORMAT"
      }
    ]
  }
}
```

### Common Validation Types

| Type       | Rule Example                                     |
| ---------- | ------------------------------------------------ |
| `required` | Field must be present                             |
| `string`   | Must be a string                                  |
| `email`    | Valid email format                                |
| `uuid`     | Valid UUID v4 format                              |
| `url`      | Valid URL                                         |
| `minLength`| Minimum string length                             |
| `maxLength`| Maximum string length                             |
| `minimum`  | Minimum numeric value                             |
| `maximum`  | Maximum numeric value                             |
| `enum`     | Must be one of allowed values                     |
| `pattern`  | Must match regex pattern                          |

## 8. Rate Limiting

### Response Headers

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640000000
Retry-After: 60
```

### Rate Limit Exceeded Response

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
Content-Type: application/json

{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded. Retry after 60 seconds.",
    "retryAfter": 60
  }
}
```

### Rate Limit Tiers

| Tier      | Requests/minute | Use Case             |
| --------- | --------------- | ------------------- |
| Standard  | 60              | Default              |
| Elevated  | 600             | Authenticated        |
| Partner   | 6000            | Business partners    |
| Internal  | Unlimited       | Service-to-service   |

## 9. Request/Response Conventions

### Content Type

Always specify Content-Type and Accept headers:

```http
Content-Type: application/json
Accept: application/json
```

### Date Format

Use **ISO 8601** with UTC timezone:

```json
{
  "createdAt": "2024-01-15T10:30:00Z",
  "updatedAt": "2024-01-15T10:30:00.000Z"
}
```

### Null vs Empty

| Value    | Meaning                                           |
| -------- | ------------------------------------------------- |
| `null`   | Field exists but has no value                     |
| `[]`     | Empty collection                                   |
| `""`     | Empty string (rarely use)                         |
| omitted  | Field not included (sparse fields)                |

### Sparse Fieldsets

Allow clients to request specific fields:

```bash
GET /users?fields=id,name,email
```

## 10. Async Operations

### Accepted Response (202)

```http
HTTP/1.1 202 Accepted
Location: /operations/12345
```

### Operation Resource

```json
{
  "id": "12345",
  "status": "processing",
  "createdAt": "2024-01-15T10:30:00Z",
  "estimatedCompletion": "2024-01-15T10:35:00Z"
}
```

### Webhook Alternative

For async operations that complete asynchronously:

```json
{
  "id": "12345",
  "status": "processing",
  "webhookUrl": "https://client.example.com/callbacks/operation/12345"
}
```

## 11. Security

### Authentication

- Use Bearer tokens in Authorization header
- API keys for server-to-server
- JWT or OAuth 2.0 for user authentication

### Authorization

- Implement RBAC or ABAC
- Validate permissions at endpoint level
- Audit log all authorization failures

### CORS

Configure appropriate CORS headers:

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization, Idempotency-Key
Access-Control-Max-Age: 86400
```

## 12. API Design Checklist

Before finalizing any REST API design:

- [ ] URLs use plural nouns, kebab-case, no verbs
- [ ] Correct HTTP methods used (GET/POST/PATCH/DELETE)
- [ ] Consistent error format with machine-readable codes
- [ ] Appropriate HTTP status codes
- [ ] Cursor-based pagination implemented
- [ ] Idempotency keys supported for POST
- [ ] Input validation at boundary with clear errors
- [ ] Rate limiting headers present
- [ ] Versioning strategy defined
- [ ] Security headers configured

---
> Source: [plutowang/agent.files](https://github.com/plutowang/agent.files) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
