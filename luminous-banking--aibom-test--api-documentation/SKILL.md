---
name: api-documentation
description: Generate and maintain API documentation for web services and libraries. Use when the user needs API docs, wants to document endpoints, or asks about OpenAPI/Swagger documentation. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# API Documentation

Generate clear, comprehensive API documentation for web services.

## Quick Start

When documenting APIs:

1. Identify all endpoints or functions
2. Document request/response formats
3. Include authentication requirements
4. Provide usage examples

## REST API Documentation Template

### Endpoint Documentation

```markdown
## [HTTP Method] /path/to/endpoint

Brief description of what this endpoint does.

### Authentication
- Required: Yes/No
- Type: Bearer Token / API Key / None

### Request

**Headers:**
| Header | Required | Description |
|--------|----------|-------------|
| Authorization | Yes | Bearer token |
| Content-Type | Yes | application/json |

**Path Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| id | string | Resource identifier |

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| limit | integer | 10 | Max results |
| offset | integer | 0 | Skip results |

**Request Body:**
\`\`\`json
{
  "name": "string",
  "email": "string"
}
\`\`\`

### Response

**Success (200 OK):**
\`\`\`json
{
  "id": "abc123",
  "name": "Example",
  "created_at": "2024-01-01T00:00:00Z"
}
\`\`\`

**Error Responses:**
| Status | Description |
|--------|-------------|
| 400 | Invalid request body |
| 401 | Unauthorized |
| 404 | Resource not found |

### Example

\`\`\`bash
curl -X POST https://api.example.com/users \
  -H "Authorization: Bearer token123" \
  -H "Content-Type: application/json" \
  -d '{"name": "John", "email": "john@example.com"}'
\`\`\`
```

## OpenAPI/Swagger Specification

For FastAPI or similar frameworks, generate OpenAPI specs:

```yaml
openapi: 3.0.0
info:
  title: API Name
  version: 1.0.0
  description: API description

paths:
  /resource:
    get:
      summary: List resources
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Resource'

components:
  schemas:
    Resource:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
```

## Python Docstring Format

For library documentation, use Google-style docstrings:

```python
def process_data(input_data: dict, options: Optional[dict] = None) -> dict:
    """Process the input data according to specified options.

    Args:
        input_data: Dictionary containing the data to process.
            Must include 'id' and 'content' keys.
        options: Optional configuration dictionary.
            - format: Output format ('json' or 'xml')
            - validate: Whether to validate input (default: True)

    Returns:
        Processed data dictionary with added metadata.

    Raises:
        ValueError: If input_data is missing required keys.
        ProcessingError: If data processing fails.

    Example:
        >>> result = process_data({'id': '1', 'content': 'test'})
        >>> print(result['status'])
        'processed'
    """
```

## Documentation Checklist

- [ ] All endpoints documented
- [ ] Request/response examples provided
- [ ] Authentication requirements clear
- [ ] Error responses documented
- [ ] Rate limits mentioned if applicable
- [ ] Versioning strategy explained

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
