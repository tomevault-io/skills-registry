---
name: rest-api-design
description: Guidelines for designing clean and standard RESTful APIs. Use when creating new controllers, routes, or defining API contracts. Use when this capability is needed.
metadata:
  author: nam088
---

# REST API Design Skill

## When to use
- When designing new endpoints.
- When reviewing API implementations.
- When the user asks for "API conventions" or "REST standards".

## Conventions

### 1. Resource Naming
- Use **plurals** for nouns: `/users`, `/products`.
- Use **hyphens** for readability: `/user-profiles`.
- Avoid verbs in URLs (e.g., avoid `/getUsers`, `/createUser`). Use HTTP methods instead.

### 2. HTTP Methods
- **GET**: Retrieve a resource. Safe & Idempotent.
- **POST**: Create a new resource. Not Idempotent.
- **PUT**: Replace a resource entirely. Idempotent.
- **PATCH**: Partial update (modify specific fields). Idempotent.
- **DELETE**: Remove a resource. Idempotent.

### 3. Status Codes
- **200 OK**: Success (GET, PUT, PATCH).
- **201 Created**: Success (POST) - should return Location header or created resource.
- **204 No Content**: Success (DELETE) - no body returned.
- **400 Bad Request**: Validation error, malformed JSON.
- **401 Unauthorized**: Missing/Invalid authentication token.
- **403 Forbidden**: Authenticated but no permission.
- **404 Not Found**: Resource does not exist.
- **429 Too Many Requests**: Rate limit exceeded.
- **500 Internal Server Error**: Unexpected server crash.

### 4. Filtering, Sorting, Pagination
- Use query parameters, not body, for GET requests.
- **Filtering**: `?status=active&role=admin`
- **Sorting**: `?sort=createdAt&order=desc`
- **Pagination**: `?page=1&limit=20` or `?offset=0&limit=20`

### 5. Response Format
- consistency is key. Always wrap lists? Or return arrays directly?
- **Standard Envelope** (Optional but common):
  ```json
  {
    "data": { ... },
    "meta": { "page": 1, "total": 100 }
  }
  ```
- **Error Format**:
  ```json
  {
    "code": "VALIDATION_ERROR",
    "message": "Email is invalid",
    "details": [...]
  }
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nam088) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
