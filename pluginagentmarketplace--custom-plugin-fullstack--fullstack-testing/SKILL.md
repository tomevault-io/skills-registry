---
name: fullstack-testing
description: Use when working with the file or component to test
metadata:
  author: pluginagentmarketplace
---

# Fullstack Testing Skill

Atomic skill for testing including unit tests, integration tests, and E2E tests.

## Responsibility

**Single Purpose**: Write and manage tests for fullstack applications

## Actions

### `write_unit_test`
Write unit tests for a component or function.

```typescript
// Input
{
  action: "write_unit_test",
  test_framework: "vitest",
  target: "src/services/user.service.ts"
}

// Output
{
  success: true,
  test_code: "describe('UserService', () => {...})",
  files: [
    { path: "src/services/user.service.test.ts", content: "..." }
  ],
  coverage: { statements: 85, branches: 80, functions: 90, lines: 85 }
}
```

### `write_integration_test`
Write integration tests for API endpoints.

### `write_e2e_test`
Write E2E tests for user flows.

### `analyze_coverage`
Analyze test coverage and identify gaps.

## Validation Rules

```typescript
function validateParams(params: SkillParams): ValidationResult {
  if (!params.action) {
    return { valid: false, error: "action is required" };
  }

  if (params.action !== 'analyze_coverage' && !params.target) {
    return { valid: false, error: "target required for test writing" };
  }

  return { valid: true };
}
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| TARGET_NOT_FOUND | Test target not found | Verify file path |
| FRAMEWORK_UNSUPPORTED | Test framework not supported | Check supported frameworks |
| COVERAGE_BELOW_THRESHOLD | Coverage below target | Add more tests |
| FLAKY_TEST_DETECTED | Test is non-deterministic | Fix race condition |

## Logging Hooks

```json
{
  "on_invoke": "log.info('fullstack-testing invoked', { action, target })",
  "on_success": "log.info('Test created', { files, coverage })",
  "on_error": "log.error('Testing skill failed', { error })"
}
```

## Unit Test Template

```typescript
import { describe, it, expect } from 'vitest';
import { fullstackTesting } from './fullstack-testing';

describe('fullstack-testing skill', () => {
  describe('write_unit_test', () => {
    it('should create test file with proper structure', async () => {
      const result = await fullstackTesting({
        action: 'write_unit_test',
        test_framework: 'vitest',
        target: 'src/services/user.service.ts'
      });

      expect(result.success).toBe(true);
      expect(result.test_code).toContain('describe');
      expect(result.test_code).toContain('it(');
    });

    it('should include arrange-act-assert pattern', async () => {
      const result = await fullstackTesting({
        action: 'write_unit_test',
        target: 'src/utils/format.ts'
      });

      expect(result.test_code).toMatch(/Arrange|Act|Assert/);
    });
  });

  describe('analyze_coverage', () => {
    it('should identify untested code paths', async () => {
      const result = await fullstackTesting({
        action: 'analyze_coverage',
        coverage_threshold: 80
      });

      expect(result.success).toBe(true);
      expect(result.coverage).toBeDefined();
    });
  });
});
```

## Integration

- **Bonded Agent**: 06-testing-strategy
- **Upstream Skills**: frontend-development, backend-development
- **Downstream Skills**: devops-fullstack

## Version History
| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01 | Initial release |
| 2.0.0 | 2025-01 | Production-grade upgrade with Playwright patterns |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
