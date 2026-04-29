---
name: backend-development
description: Use when working with the specific backend action to perform
metadata:
  author: pluginagentmarketplace
---

# Backend Development Skill

Atomic skill for backend development including API creation, authentication, and service implementation.

## Responsibility

**Single Purpose**: Implement backend services, APIs, and business logic

## Actions

### `create_endpoint`
Create a new API endpoint with validation and error handling.

```typescript
// Input
{
  action: "create_endpoint",
  runtime: "nodejs",
  framework: "express",
  api_style: "rest"
}

// Output
{
  success: true,
  code: "router.post('/users', validate(schema), async (req, res) => {...})",
  files: [
    { path: "routes/users.ts", content: "..." },
    { path: "routes/users.test.ts", content: "..." }
  ],
  api_spec: { openapi: "3.0.0", paths: {...} },
  security_notes: ["Rate limiting recommended", "Input validation applied"]
}
```

### `implement_auth`
Implement authentication and authorization.

### `build_service`
Build a business logic service.

### `integrate_external`
Integrate with external APIs.

## Validation Rules

```typescript
function validateParams(params: SkillParams): ValidationResult {
  if (!params.action) {
    return { valid: false, error: "action is required" };
  }

  if (params.action === 'implement_auth' && !params.runtime) {
    return { valid: false, error: "runtime required for auth implementation" };
  }

  return { valid: true };
}
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| INVALID_RUNTIME | Unsupported runtime | Check supported runtimes |
| AUTH_PATTERN_INSECURE | Security vulnerability detected | Apply secure pattern |
| API_DESIGN_VIOLATION | REST/GraphQL best practice violation | Suggest correction |

## Logging Hooks

```json
{
  "on_invoke": "log.info('backend-development invoked', { action, runtime })",
  "on_success": "log.info('Endpoint created', { files, api_spec })",
  "on_error": "log.error('Backend skill failed', { error })"
}
```

## Unit Test Template

```typescript
import { describe, it, expect } from 'vitest';
import { backendDevelopment } from './backend-development';

describe('backend-development skill', () => {
  describe('create_endpoint', () => {
    it('should create REST endpoint with validation', async () => {
      const result = await backendDevelopment({
        action: 'create_endpoint',
        runtime: 'nodejs',
        framework: 'express',
        api_style: 'rest'
      });

      expect(result.success).toBe(true);
      expect(result.code).toContain('validate');
      expect(result.api_spec.openapi).toBe('3.0.0');
    });

    it('should include security middleware', async () => {
      const result = await backendDevelopment({
        action: 'create_endpoint',
        runtime: 'nodejs'
      });

      expect(result.code).toMatch(/authenticate|rateLimiter/);
    });
  });

  describe('implement_auth', () => {
    it('should implement JWT auth with refresh tokens', async () => {
      const result = await backendDevelopment({
        action: 'implement_auth',
        runtime: 'nodejs'
      });

      expect(result.success).toBe(true);
      expect(result.security_notes.length).toBeGreaterThan(0);
    });
  });
});
```

## Integration

- **Bonded Agent**: 03-backend-development
- **Upstream Skills**: fullstack-basics
- **Downstream Skills**: database-integration, fullstack-testing

## Version History
| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01 | Initial release |
| 2.0.0 | 2025-01 | Production-grade upgrade with security patterns |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
