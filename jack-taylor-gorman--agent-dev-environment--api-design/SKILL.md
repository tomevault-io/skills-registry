---
name: api-design
description: Design RESTful and GraphQL APIs following industry best practices for consistency, usability, and maintainability. Use when this capability is needed.
metadata:
  author: jack-taylor-gorman
---

# API Design Protocol

When designing APIs:

## REST API Design

1. **Resource Naming**
   - Use nouns, not verbs: `/users`, not `/getUsers`
   - Use plural for collections: `/articles`, `/comments`
   - Nest resources logically: `/users/{id}/posts`

2. **HTTP Methods**
   - `GET`: Retrieve resources (idempotent, cacheable)
   - `POST`: Create new resources
   - `PUT`: Replace entire resource
   - `PATCH`: Partial update
   - `DELETE`: Remove resource

3. **Status Codes**
   - `200 OK`: Successful GET, PUT, PATCH
   - `201 Created`: Successful POST
   - `204 No Content`: Successful DELETE
   - `400 Bad Request`: Invalid input
   - `401 Unauthorized`: Missing/invalid auth
   - `403 Forbidden`: Authenticated but not authorized
   - `404 Not Found`: Resource doesn't exist
   - `500 Internal Server Error`: Server failure

4. **Response Format**
   - Consistent JSON structure
   - Include metadata (pagination, timestamps)
   - Use camelCase or snake_case consistently
   - Provide meaningful error messages

5. **Versioning**
   - URL versioning: `/v1/users`, `/v2/users`
   - Header versioning: `Accept: application/vnd.api.v2+json`
   - Maintain backward compatibility when possible

## GraphQL Design

1. **Schema Design**
   - Define clear types and relationships
   - Use interfaces for polymorphic types
   - Implement pagination (cursor-based preferred)

2. **Query Optimization**
   - Implement DataLoader to prevent N+1 queries
   - Use query complexity analysis
   - Set depth and rate limits

3. **Error Handling**
   - Return partial data when possible
   - Use extensions for error metadata
   - Distinguish user errors from system errors

## Best Practices

- **Pagination**: Use cursor-based for large datasets
- **Filtering & Sorting**: Support query parameters
- **Authentication**: Use OAuth2 or JWT
- **Rate Limiting**: Protect against abuse
- **Documentation**: Use OpenAPI (Swagger) or GraphQL introspection
- **Validation**: Validate input at API boundary
- **CORS**: Configure properly for web clients

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jack-taylor-gorman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
