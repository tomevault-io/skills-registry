---
name: documentation
description: API documentation with OpenAPI and developer portals Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# API Documentation Skill

## Purpose
Create comprehensive API documentation.

## OpenAPI 3.1 Structure

```yaml
openapi: 3.1.0
info:
  title: My API
  version: 1.0.0
  description: |
    API description with **markdown** support.

    ## Authentication
    Use Bearer tokens.

servers:
  - url: https://api.example.com
    description: Production

paths:
  /users:
    get:
      operationId: listUsers
      summary: List all users
      tags: [Users]
      parameters:
        - $ref: '#/components/parameters/PageParam'
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'

components:
  schemas:
    User:
      type: object
      required: [id, name]
      properties:
        id:
          type: string
          format: uuid
        name:
          type: string
```

## Code Examples

### Multiple Languages

```yaml
# In OpenAPI
paths:
  /users:
    post:
      x-codeSamples:
        - lang: curl
          source: |
            curl -X POST https://api.example.com/users \
              -H "Authorization: Bearer TOKEN" \
              -d '{"name": "John"}'
        - lang: python
          source: |
            import requests
            requests.post('https://api.example.com/users',
              headers={'Authorization': 'Bearer TOKEN'},
              json={'name': 'John'})
        - lang: javascript
          source: |
            await fetch('https://api.example.com/users', {
              method: 'POST',
              headers: { 'Authorization': 'Bearer TOKEN' },
              body: JSON.stringify({ name: 'John' })
            });
```

## SDK Generation

```bash
# Generate TypeScript SDK
npx @openapitools/openapi-generator-cli generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdk

# Generate Python SDK
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -o ./sdk-python
```

## Changelog Format

```markdown
# Changelog

## [1.2.0] - 2024-12-30

### Added
- `GET /users/export` endpoint

### Changed
- `POST /users` now returns 201

### Deprecated
- `GET /users/:id/profile` (use `/users/:id`)

### Fixed
- Pagination cursor encoding
```

---

## Unit Test Template

```typescript
import { validate } from '@apidevtools/swagger-parser';

describe('OpenAPI Spec', () => {
  it('should be valid OpenAPI 3.1', async () => {
    const api = await validate('./openapi.yaml');
    expect(api.openapi).toMatch(/^3\.1\./);
  });

  it('should have examples for all endpoints', async () => {
    const api = await validate('./openapi.yaml');
    Object.values(api.paths).forEach(path => {
      Object.values(path).forEach(operation => {
        if (operation.responses?.['200']) {
          expect(operation.responses['200'].content)
            .toHaveProperty('application/json.examples');
        }
      });
    });
  });
});
```

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Spec validation fails | Invalid syntax | Use spectral linter |
| SDK types wrong | Schema mismatch | Regenerate from spec |
| Missing examples | Incomplete docs | Add request/response examples |

---

## Quality Checklist

- [ ] OpenAPI spec validates
- [ ] All endpoints documented
- [ ] Request/response examples
- [ ] Error responses documented
- [ ] Authentication explained
- [ ] SDKs generated and tested

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
