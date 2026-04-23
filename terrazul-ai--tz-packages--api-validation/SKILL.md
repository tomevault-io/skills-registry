---
name: api-validation
description: Comprehensive API endpoint validation including schema validation, authentication testing, and error handling Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# API Validation Skill

Thorough validation of API endpoints including schema compliance, authentication, authorization, error handling, and performance.

## When to Use

- Validating new API endpoints
- Testing API changes
- Schema compliance verification
- Authentication/authorization testing
- API contract validation
- Performance baseline checks

## Capabilities

1. **Schema validation** - Verify responses match expected structure
2. **Authentication testing** - Test auth flows and token handling
3. **Authorization testing** - Verify permission enforcement
4. **Error handling** - Validate error responses and messages
5. **Performance testing** - Check response times and limits

## Workflow

### Phase 1: Discovery

Understand the API:
- Review documentation (OpenAPI, Swagger, README)
- Identify endpoints to validate
- Note authentication requirements
- Document expected responses

### Phase 2: Test Design

Create comprehensive test cases:

**For Each Endpoint:**
- Success scenarios (valid requests)
- Authentication tests (valid/invalid/missing token)
- Authorization tests (permitted/forbidden actions)
- Validation tests (required fields, formats, types)
- Error scenarios (not found, server errors)

### Phase 3: Execution

Run tests systematically:
1. Test authentication first
2. Test each endpoint method
3. Validate response schemas
4. Check error handling
5. Measure performance

### Phase 4: Validation

For each response, verify:
- Status code correct
- Response body matches schema
- Required fields present
- Data types correct
- Headers appropriate
- Timing acceptable

### Phase 5: Reporting

Document findings:
- Endpoints validated
- Tests passed/failed
- Schema violations
- Security issues
- Performance concerns

## Validation Checklist

### Authentication Validation
- [ ] Unauthenticated requests rejected (401)
- [ ] Invalid tokens rejected (401)
- [ ] Expired tokens rejected (401)
- [ ] Valid tokens accepted (200)
- [ ] Token refresh works (if applicable)

### Authorization Validation
- [ ] Users can access own resources
- [ ] Users cannot access others' resources (403)
- [ ] Admin has elevated access
- [ ] Role-based permissions enforced

### Schema Validation
- [ ] Response structure matches schema
- [ ] Required fields present
- [ ] Data types correct (string, number, boolean)
- [ ] Enum values valid
- [ ] Nested objects correct
- [ ] Arrays typed correctly

### Input Validation
- [ ] Required fields enforced (400)
- [ ] Type validation works (400)
- [ ] Format validation works - email, URL, etc. (400)
- [ ] Length limits enforced (400)
- [ ] Range limits enforced (400)
- [ ] Unique constraints enforced (409)

### Error Handling Validation
- [ ] 400 for bad requests
- [ ] 401 for unauthorized
- [ ] 403 for forbidden
- [ ] 404 for not found
- [ ] 409 for conflicts
- [ ] 500 errors don't leak info
- [ ] Error format consistent
- [ ] Error messages helpful

### Performance Validation
- [ ] Response time < acceptable threshold
- [ ] Pagination works correctly
- [ ] Large responses handled
- [ ] Rate limiting works (if applicable)

## Common Validation Patterns

### CRUD Endpoint Validation
```
Use the api-validation skill to validate all CRUD operations on the /api/users endpoint
```

Tests:
- POST /users - Create with valid data (201)
- POST /users - Create with invalid data (400)
- GET /users - List all (200, paginated)
- GET /users/:id - Get existing (200)
- GET /users/:id - Get non-existing (404)
- PUT /users/:id - Update existing (200)
- PUT /users/:id - Update non-existing (404)
- DELETE /users/:id - Delete existing (204)
- DELETE /users/:id - Delete non-existing (404)

### Auth Endpoint Validation
```
Use the api-validation skill to thoroughly test authentication on /api/auth endpoints
```

Tests:
- POST /auth/login - Valid credentials (200 + token)
- POST /auth/login - Invalid password (401)
- POST /auth/login - Unknown user (401)
- POST /auth/logout - With valid token (200)
- POST /auth/refresh - Valid refresh token (200)
- POST /auth/refresh - Invalid token (401)

### Protected Endpoint Validation
```
Use the api-validation skill to verify authorization on /api/admin endpoints
```

Tests:
- Request without token (401)
- Request with user token (403)
- Request with admin token (200)
- Request with expired token (401)

## Schema Validation Approach

For each response field, verify:

```markdown
| Field | Type | Required | Validation |
|-------|------|----------|------------|
| id | string | yes | UUID format |
| email | string | yes | email format |
| name | string | yes | min 1, max 100 |
| status | string | yes | enum: active, inactive |
| createdAt | string | yes | ISO date format |
| metadata | object | no | can be empty |
```

## Usage Examples

### Full Endpoint Validation
```
Use the api-validation skill to thoroughly validate the /api/products endpoint including all HTTP methods, authentication, and error handling
```

### Auth-Focused Validation
```
Use the api-validation skill to test authentication and authorization across all protected endpoints
```

### Schema Compliance Check
```
Use the api-validation skill to verify all API responses match the OpenAPI schema
```

### Input Validation Testing
```
Use the api-validation skill to test input validation on POST /api/orders with various invalid payloads
```

## Validation Report Format

```markdown
# API Validation Report: [Endpoint]

**Date**: [Date]
**Endpoint**: [PATH]
**Methods Tested**: GET, POST, PUT, DELETE

## Summary

| Category | Tests | Passed | Failed |
|----------|-------|--------|--------|
| Authentication | X | X | X |
| Authorization | X | X | X |
| Schema | X | X | X |
| Validation | X | X | X |
| Error Handling | X | X | X |
| Performance | X | X | X |

## Detailed Results

### Authentication Tests
[Results table]

### Schema Validation
[Field-by-field results]

### Issues Found
[List with severity]

## Recommendations
[Action items]
```

## Best Practices

1. **Start with auth** - Test authentication before other endpoints
2. **Use real payloads** - Test with production-like data
3. **Test all methods** - GET, POST, PUT, PATCH, DELETE
4. **Validate schemas strictly** - Every field, every type
5. **Check error formats** - Consistency matters
6. **Test edge cases** - Empty strings, null, max lengths
7. **Measure performance** - Set baselines, track changes
8. **Document findings** - Include evidence (requests/responses)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
