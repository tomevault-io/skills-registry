---
name: db-migration-reviewer
description: Review Drizzle ORM migrations for MySQL. Checks for potential data loss, performance issues, missing indexes, and schema alignment. Use when reviewing changes in the drizzle/ directory or schema.ts files. Use when this capability is needed.
metadata:
  author: gregsuptown
---

# Database Migration Reviewer (MySQL/Drizzle)

## Standards

### Schema Design
- ✅ Use appropriate column types (e.g., `varchar(255)` for emails, `text` for long content)
- ✅ Ensure primary keys are defined for every table
- ✅ Use `timestamp` for created/updated fields with appropriate defaults
- ✅ Define foreign key constraints for relationships

### Performance
- ✅ Ensure columns used in WHERE clauses are indexed
- ✅ Avoid over-indexing (limit to essential query patterns)
- ✅ Check for full table scans in proposed query changes

### Safety
- ❌ No `DROP TABLE` or `DROP COLUMN` without explicit confirmation
- ❌ No renaming columns without a multi-step migration strategy (if in production)
- ✅ Ensure default values are set for new NOT NULL columns

### Drizzle Specifics
- ✅ Ensure `drizzle-kit generate` was run after schema changes
- ✅ Check that migrations are sequential and don't conflict

## Review Checklist

1. **Data Loss:** Does this migration delete or modify existing data?
2. **Indexing:** Are the new tables/columns properly indexed for their intended use?
3. **Naming:** Do table and column names follow the project's camelCase or snake_case convention?
4. **Types:** Are the column types optimal for the data they will store?

## Auto-Invocation Triggers

This Skill should activate when:
- Reviewing files in `drizzle/` folder
- Reviewing `server/db/schema.ts` or similar schema files
- User asks to "check database changes" or "review migration"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gregsuptown) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
