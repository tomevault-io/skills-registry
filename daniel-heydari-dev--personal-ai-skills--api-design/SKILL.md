---
name: api-design
description: RESTful API design conventions, OpenAPI specifications, versioning strategies, and error response patterns. Use when designing, building, or documenting APIs. Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: API Design

Design professional, consistent, and developer-friendly APIs.

## REST Principles

### Rules

- ✅ DO: Use nouns for resources, not verbs
- ✅ DO: Use plural nouns for collections
- ✅ DO: Use HTTP methods correctly
- ✅ DO: Return appropriate status codes
- ✅ DO: Version your API
- ❌ DON'T: Use verbs in URLs (`/getUsers`, `/createUser`)
- ❌ DON'T: Use inconsistent naming
- ❌ DON'T: Return 200 for errors
- ❌ DON'T: Break backward compatibility

## URL Design

### Resource Naming

```
# Collections (plural nouns)
GET    /users              # List users
POST   /users              # Create user
GET    /users/{id}         # Get user
PUT    /users/{id}         # Update user
DELETE /users/{id}         # Delete user

# Nested resources
GET    /users/{id}/orders         # User's orders
GET    /users/{id}/orders/{oid}   # Specific order

# Relationships
GET    /orders/{id}/items         # Order items
POST   /orders/{id}/items         # Add item to order
```

### URL Conventions

```
✅ Good URLs:
/api/v1/users
/api/v1/users/123
/api/v1/users/123/orders
/api/v1/products?category=electronics&sort=price

❌ Bad URLs:
/api/v1/getUsers
/api/v1/user/create
/api/v1/users/123/getOrders
/api/v1/products/findByCategory/electronics
```

### Query Parameters

```
# Filtering
GET /products?status=active&category=electronics

# Sorting
GET /products?sort=price&order=desc
GET /products?sort=-price,+name  # Alternative: prefix notation

# Pagination
GET /products?page=2&limit=20
GET /products?offset=20&limit=20  # Alternative: offset

# Field selection
GET /users/123?fields=id,name,email

# Search
GET /products?q=laptop&search_fields=name,description
```

## HTTP Methods

### Method Semantics

| Method | Purpose          | Idempotent | Safe | Request Body |
| ------ | ---------------- | ---------- | ---- | ------------ |
| GET    | Read resource    | Yes        | Yes  | No           |
| POST   | Create resource  | No         | No   | Yes          |
| PUT    | Replace resource | Yes        | No   | Yes          |
| PATCH  | Partial update   | No\*       | No   | Yes          |
| DELETE | Remove resource  | Yes        | No   | No           |

### Usage Examples

```typescript
// GET - Retrieve (never modify data)
GET /users/123
// Response: 200 OK with user data

// POST - Create new resource
POST /users
Content-Type: application/json
{ "name": "John", "email": "john@example.com" }
// Response: 201 Created with Location header

// PUT - Full replacement (send all fields)
PUT /users/123
Content-Type: application/json
{ "name": "John Doe", "email": "john@example.com", "role": "admin" }
// Response: 200 OK

// PATCH - Partial update (send only changed fields)
PATCH /users/123
Content-Type: application/json
{ "role": "admin" }
// Response: 200 OK

// DELETE - Remove resource
DELETE /users/123
// Response: 204 No Content
```

## Status Codes

### Success Codes (2xx)

```
200 OK           - Successful GET, PUT, PATCH
201 Created      - Successful POST (include Location header)
202 Accepted     - Request accepted for async processing
204 No Content   - Successful DELETE (no body returned)
```

### Client Error Codes (4xx)

```
400 Bad Request     - Invalid request body/params
401 Unauthorized    - Missing/invalid authentication
403 Forbidden       - Authenticated but not authorized
404 Not Found       - Resource doesn't exist
405 Method Not Allowed - HTTP method not supported
409 Conflict        - Resource conflict (duplicate, etc.)
422 Unprocessable   - Validation errors
429 Too Many Requests - Rate limit exceeded
```

### Server Error Codes (5xx)

```
500 Internal Error  - Unexpected server error
502 Bad Gateway     - Upstream service error
503 Service Unavailable - Temporarily unavailable
504 Gateway Timeout - Upstream timeout
```

## Error Responses

### Standard Error Format

```typescript
interface ErrorResponse {
  error: {
    code: string; // Machine-readable error code
    message: string; // Human-readable message
    details?: ErrorDetail[]; // Field-level errors
    requestId?: string; // For support/debugging
  };
}

interface ErrorDetail {
  field: string;
  message: string;
  code: string;
}
```

### Error Examples

```json
// 400 Bad Request - Validation errors
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
        "field": "password",
        "message": "Password must be at least 8 characters",
        "code": "MIN_LENGTH"
      }
    ],
    "requestId": "req_abc123"
  }
}

// 404 Not Found
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "User with ID 123 not found",
    "requestId": "req_xyz789"
  }
}

// 401 Unauthorized
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired authentication token"
  }
}

// 429 Rate Limited
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Too many requests. Please retry after 60 seconds"
  }
}
```

## Pagination

### Cursor-Based (Recommended)

```json
// Request
GET /orders?limit=20&cursor=eyJpZCI6MTAwfQ

// Response
{
  "data": [...],
  "pagination": {
    "hasMore": true,
    "nextCursor": "eyJpZCI6MTIwfQ",
    "prevCursor": "eyJpZCI6ODB9"
  }
}
```

### Offset-Based

```json
// Request
GET /orders?page=2&limit=20

// Response
{
  "data": [...],
  "pagination": {
    "page": 2,
    "limit": 20,
    "totalPages": 10,
    "totalItems": 200
  }
}
```

### Link Headers (RFC 5988)

```
Link: <https://api.example.com/orders?page=3&limit=20>; rel="next",
      <https://api.example.com/orders?page=1&limit=20>; rel="prev",
      <https://api.example.com/orders?page=10&limit=20>; rel="last"
```

## Versioning

### URL Path Versioning (Recommended)

```
GET /api/v1/users
GET /api/v2/users
```

### Header Versioning

```
GET /api/users
Accept: application/vnd.api+json; version=2
```

### Versioning Rules

- ✅ DO: Version from day one
- ✅ DO: Support at least one previous version
- ✅ DO: Document deprecation timeline
- ✅ DO: Use semantic versioning principles
- ❌ DON'T: Break backward compatibility within a version
- ❌ DON'T: Remove fields without deprecation period

## Request/Response Patterns

### Request Format

```typescript
// Create request
POST /users
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user"
}

// Update request (PATCH)
PATCH /users/123
Content-Type: application/json

{
  "role": "admin"  // Only changed fields
}
```

### Response Format

```typescript
// Single resource
{
  "data": {
    "id": "123",
    "type": "user",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com",
      "createdAt": "2024-01-15T10:30:00Z"
    }
  }
}

// Collection
{
  "data": [
    { "id": "1", "name": "..." },
    { "id": "2", "name": "..." }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100
  }
}
```

## OpenAPI Specification

### Basic Structure

```yaml
openapi: 3.1.0
info:
  title: My API
  version: 1.0.0
  description: API for managing users and orders

servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging

paths:
  /users:
    get:
      summary: List users
      operationId: listUsers
      tags: [Users]
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
            maximum: 100
      responses:
        "200":
          description: List of users
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/UserList"

components:
  schemas:
    User:
      type: object
      required: [id, email, name]
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 1
          maxLength: 100
        createdAt:
          type: string
          format: date-time
```

## Authentication

### Bearer Token

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

### API Key

```
# Header
X-API-Key: your-api-key

# Query parameter (not recommended for sensitive APIs)
GET /data?api_key=your-api-key
```

### Security Best Practices

- ✅ DO: Use HTTPS everywhere
- ✅ DO: Use short-lived access tokens
- ✅ DO: Implement refresh token rotation
- ✅ DO: Rate limit authentication endpoints
- ❌ DON'T: Send credentials in URL
- ❌ DON'T: Use long-lived API keys for public clients

## Rate Limiting

### Headers

```
X-RateLimit-Limit: 1000        # Requests per window
X-RateLimit-Remaining: 950      # Remaining requests
X-RateLimit-Reset: 1640000000   # Unix timestamp when limit resets
Retry-After: 60                 # Seconds to wait (when 429)
```

### Response When Limited

```json
HTTP/1.1 429 Too Many Requests
Retry-After: 60

{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded. Try again in 60 seconds.",
    "retryAfter": 60
  }
}
```

## HATEOAS (Hypermedia)

```json
{
  "data": {
    "id": "123",
    "status": "pending",
    "total": 99.99
  },
  "links": {
    "self": "/orders/123",
    "cancel": "/orders/123/cancel",
    "pay": "/orders/123/pay",
    "items": "/orders/123/items"
  },
  "actions": {
    "cancel": {
      "method": "POST",
      "href": "/orders/123/cancel"
    },
    "pay": {
      "method": "POST",
      "href": "/orders/123/pay",
      "fields": [
        { "name": "paymentMethod", "type": "string", "required": true }
      ]
    }
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
