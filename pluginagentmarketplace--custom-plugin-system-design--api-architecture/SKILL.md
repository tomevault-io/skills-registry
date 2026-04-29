---
name: api-architecture
description: Production-grade API architecture skill for REST, gRPC, GraphQL design, versioning, and security patterns Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# API Architecture Skill

> **Purpose**: Atomic skill for API design with comprehensive validation, error handling, and security patterns.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | REST, gRPC, GraphQL Design |
| **Responsibility** | Single: API contract and protocol design |
| **Invocation** | `Skill("api-architecture")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  api_context:
    type: object
    required: true
    properties:
      protocol:
        type: string
        enum: [REST, gRPC, GraphQL, hybrid]
        required: true
      resources:
        type: array
        items:
          type: object
          properties:
            name: { type: string, pattern: "^[a-z][a-z0-9-]*$" }
            operations: { type: array, items: { type: string } }
        minItems: 1
      consumers:
        type: array
        items:
          type: string
          enum: [web, mobile, third_party, internal, iot]
      security:
        type: object
        properties:
          auth_method: { type: string, enum: [oauth2, api_key, jwt, mtls] }
          rate_limiting: { type: boolean, default: true }
      constraints:
        type: object
        properties:
          max_latency_ms: { type: integer, minimum: 1 }
          backward_compatible: { type: boolean, default: true }

validation_rules:
  - name: "resource_naming"
    rule: "resources[*].name matches /^[a-z][a-z0-9-]*$/"
    error: "Resource names must be lowercase with hyphens"
  - name: "public_api_security"
    rule: "consumers includes 'third_party' implies auth_method is set"
    error: "Public APIs require authentication"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    endpoints:
      type: array
      items:
        type: object
        properties:
          method: { type: string }
          path: { type: string }
          request_schema: { type: object }
          response_schema: { type: object }
          status_codes: { type: array }
    openapi_spec:
      type: string
      format: yaml
    security_config:
      type: object
      properties:
        auth: { type: object }
        rate_limits: { type: object }
        cors: { type: object }
```

## Core Patterns

### REST Design Principles
```
Resource Naming:
├── Nouns, plural: /users, /orders
├── Hierarchical: /users/{id}/orders
├── Actions as sub-resources: /orders/{id}/cancel
├── Query params for filtering: ?status=active
└── Kebab-case: /user-profiles

HTTP Methods:
├── GET     → Read (idempotent, cacheable)
├── POST    → Create (not idempotent)
├── PUT     → Replace (idempotent)
├── PATCH   → Update partial (idempotent)
├── DELETE  → Remove (idempotent)
├── HEAD    → Metadata only
└── OPTIONS → CORS preflight

Status Codes:
├── 200 OK: Successful GET/PUT/PATCH
├── 201 Created: Successful POST (with Location header)
├── 204 No Content: Successful DELETE
├── 400 Bad Request: Validation error
├── 401 Unauthorized: Not authenticated
├── 403 Forbidden: Not authorized
├── 404 Not Found: Resource missing
├── 409 Conflict: State conflict
├── 422 Unprocessable: Semantic error
├── 429 Too Many Requests: Rate limited
├── 500 Internal Error: Server bug
├── 502 Bad Gateway: Upstream error
├── 503 Unavailable: Maintenance
└── 504 Gateway Timeout: Upstream timeout
```

### gRPC Patterns
```
Service Types:
├── Unary: request → response
├── Server Streaming: request → stream<response>
├── Client Streaming: stream<request> → response
└── Bidirectional: stream<request> → stream<response>

Proto Best Practices:
├── Use proto3
├── Package: company.service.v1
├── Reserve deleted field numbers
├── Use well-known types
└── Backward compatible changes only
```

### GraphQL Patterns
```
Schema Design:
├── Types: Clear domain modeling
├── Queries: Read operations
├── Mutations: Write operations
├── Subscriptions: Real-time
└── Input types: Separate from output

Security:
├── Query depth limiting
├── Complexity analysis
├── Field-level authorization
├── Persisted queries
└── Rate limiting by complexity
```

## Retry Logic

### Client Retry Configuration
```yaml
retry_config:
  http_client:
    max_attempts: 3
    initial_delay_ms: 100
    max_delay_ms: 10000
    multiplier: 2.0
    jitter_factor: 0.2

  retryable_status_codes:
    - 429  # Rate limited (use Retry-After)
    - 502  # Bad gateway
    - 503  # Service unavailable
    - 504  # Gateway timeout

  non_retryable:
    - 400  # Bad request
    - 401  # Unauthorized
    - 403  # Forbidden
    - 404  # Not found
    - 409  # Conflict
    - 422  # Unprocessable

  idempotency:
    header: "Idempotency-Key"
    required_for: [POST, PATCH]
    cache_ttl_seconds: 86400
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "api-architecture" }
  request_id: { type: string }
  event:
    type: string
    enum:
      - endpoint_designed
      - schema_generated
      - validation_passed
      - security_configured
      - rate_limit_set
  context:
    type: object
    properties:
      method: { type: string }
      path: { type: string }
      protocol: { type: string }

example:
  level: INFO
  request_id: "req_abc123"
  event: endpoint_designed
  context:
    method: POST
    path: /v1/users
    protocol: REST
```

### Metrics
```yaml
metrics:
  - name: api_request_total
    type: counter
    labels: [method, path, status]

  - name: api_request_duration_seconds
    type: histogram
    labels: [method, path]
    buckets: [0.005, 0.01, 0.05, 0.1, 0.5, 1, 5]

  - name: api_rate_limit_exceeded_total
    type: counter
    labels: [client_id, path]

  - name: api_error_total
    type: counter
    labels: [method, path, error_code]
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| 400 errors | Schema mismatch | Validate against OpenAPI |
| 401 errors | Token expired | Implement refresh flow |
| 429 errors | Rate limit hit | Implement backoff, cache |
| CORS errors | Missing headers | Configure allowed origins |
| Slow response | N+1 queries | Batch, DataLoader |

### Debug Checklist
```
□ Request matches OpenAPI spec?
□ Auth token valid and not expired?
□ Rate limit headers checked?
□ Error response format consistent?
□ Retry-After header on 429/503?
□ CORS configured for all origins?
□ Pagination implemented?
```

## Unit Test Templates

### API Validation Tests
```python
# test_api_architecture.py

def test_valid_rest_resource():
    params = {
        "api_context": {
            "protocol": "REST",
            "resources": [
                {"name": "users", "operations": ["list", "get", "create", "update", "delete"]},
                {"name": "orders", "operations": ["list", "get", "create"]}
            ],
            "consumers": ["web", "mobile"],
            "security": {"auth_method": "oauth2", "rate_limiting": True}
        }
    }
    result = validate_parameters(params)
    assert result.valid == True

def test_invalid_resource_naming():
    params = {
        "api_context": {
            "protocol": "REST",
            "resources": [
                {"name": "Users", "operations": ["list"]}  # Should be lowercase
            ]
        }
    }
    result = validate_parameters(params)
    assert result.valid == False
    assert "lowercase" in result.errors[0]

def test_public_api_requires_auth():
    params = {
        "api_context": {
            "protocol": "REST",
            "resources": [{"name": "data", "operations": ["list"]}],
            "consumers": ["third_party"]
            # Missing security.auth_method
        }
    }
    result = validate_parameters(params)
    assert result.valid == False
    assert "authentication" in result.errors[0]

def test_endpoint_generation():
    resource = {"name": "users", "operations": ["list", "get", "create"]}
    endpoints = generate_endpoints(resource)

    assert endpoints[0] == {"method": "GET", "path": "/v1/users"}
    assert endpoints[1] == {"method": "GET", "path": "/v1/users/{id}"}
    assert endpoints[2] == {"method": "POST", "path": "/v1/users"}
```

### Status Code Tests
```python
def test_status_code_for_create():
    operation = "create"
    result = determine_status_codes(operation)
    assert 201 in result.success
    assert 400 in result.client_error
    assert 409 in result.client_error  # Conflict for duplicate

def test_rate_limit_headers():
    response = generate_rate_limit_headers(
        limit=1000,
        remaining=998,
        reset_epoch=1640995200
    )
    assert response["X-RateLimit-Limit"] == "1000"
    assert response["X-RateLimit-Remaining"] == "998"
    assert response["X-RateLimit-Reset"] == "1640995200"

def test_error_response_format():
    error = generate_error_response(
        code="VALIDATION_ERROR",
        message="Invalid email format",
        details=[{"field": "email", "reason": "must be valid email"}],
        request_id="req_123"
    )
    assert error["error"]["code"] == "VALIDATION_ERROR"
    assert error["error"]["request_id"] == "req_123"
    assert len(error["error"]["details"]) == 1
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with security patterns |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
