---
name: database-integration
description: Use when working with the specific database action to perform
metadata:
  author: pluginagentmarketplace
---

# Database Integration Skill

Atomic skill for database operations including schema design, query writing, and performance optimization.

## Responsibility

**Single Purpose**: Design and implement database schemas, queries, and migrations

## Actions

### `design_schema`
Design database schema with proper normalization and indexes.

```typescript
// Input
{
  action: "design_schema",
  database_type: "postgresql",
  orm: "prisma"
}

// Output
{
  success: true,
  schema: {
    models: ["User", "Post", "Comment"],
    relationships: ["User hasMany Post", "Post hasMany Comment"],
    indexes: ["users_email_idx", "posts_author_idx"]
  },
  performance_notes: ["Composite index recommended for posts queries"]
}
```

### `write_query`
Write optimized database queries.

### `create_migration`
Create database migration scripts.

### `optimize_performance`
Analyze and optimize query performance.

## Validation Rules

```typescript
function validateParams(params: SkillParams): ValidationResult {
  if (!params.action) {
    return { valid: false, error: "action is required" };
  }

  if (params.action === 'write_query' && !params.query_type) {
    return { valid: false, error: "query_type required for write_query" };
  }

  return { valid: true };
}
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| INVALID_DATABASE | Unsupported database type | Check supported databases |
| N_PLUS_ONE_DETECTED | N+1 query pattern found | Add eager loading |
| MISSING_INDEX | Query would cause full table scan | Add index recommendation |
| NORMALIZATION_VIOLATION | Schema violates 3NF | Suggest refactoring |

## Logging Hooks

```json
{
  "on_invoke": "log.info('database-integration invoked', { action, database_type })",
  "on_success": "log.info('Database operation completed', { schema, performance_notes })",
  "on_error": "log.error('Database skill failed', { error })"
}
```

## Unit Test Template

```typescript
import { describe, it, expect } from 'vitest';
import { databaseIntegration } from './database-integration';

describe('database-integration skill', () => {
  describe('design_schema', () => {
    it('should create normalized schema', async () => {
      const result = await databaseIntegration({
        action: 'design_schema',
        database_type: 'postgresql',
        orm: 'prisma'
      });

      expect(result.success).toBe(true);
      expect(result.schema.indexes.length).toBeGreaterThan(0);
    });

    it('should recommend indexes for foreign keys', async () => {
      const result = await databaseIntegration({
        action: 'design_schema',
        database_type: 'postgresql'
      });

      expect(result.schema.indexes).toContain(expect.stringMatching(/_idx$/));
    });
  });

  describe('optimize_performance', () => {
    it('should detect N+1 queries', async () => {
      const result = await databaseIntegration({
        action: 'optimize_performance',
        database_type: 'postgresql'
      });

      expect(result.success).toBe(true);
      expect(result.performance_notes).toBeDefined();
    });
  });
});
```

## Integration

- **Bonded Agent**: 04-database-design
- **Upstream Skills**: backend-development
- **Downstream Skills**: fullstack-testing

## Version History
| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01 | Initial release |
| 2.0.0 | 2025-01 | Production-grade upgrade with query optimization |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
