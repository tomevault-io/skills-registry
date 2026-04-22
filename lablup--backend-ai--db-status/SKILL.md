---
name: db-status
description: Show database schema migration status (alembic current, alembic heads, pending migrations) for Backend.AI components (manager, accountmgr, appproxy) Use when this capability is needed.
metadata:
  author: lablup
---

## Purpose

Check the current database schema version and migration status for Backend.AI components.
Use this before running migrations or when troubleshooting database issues.

**When to use:**
- Before applying migrations
- After pulling code changes
- When debugging database-related errors
- To verify migration status across components

## Parameters

- **component** (optional): Target component
  - `manager` (default) - Main manager database
  - `accountmgr` - Account manager database
  - `appproxy` - Application proxy database

## Execution Steps

1. **Identify target component and alembic config**
   - manager → `alembic.ini`
   - accountmgr → `alembic-accountmgr.ini`
   - appproxy → `alembic-appproxy.ini`

2. **Check current database revision**
   ```bash
   ./backend.ai mgr dbschema show
   ```

3. **Parse output for:**
   - Current revision (database state)
   - Head revision (latest migration)
   - Migration status

4. **Determine status:**
   - ✅ **Up-to-date**: Current revision matches head
   - ⚠️ **Behind**: Current revision older than head (migrations pending)
   - 🔀 **Diverged**: Multiple heads detected (rebase needed)
   - ❓ **Unknown**: No revision found (initial setup needed)

5. **Format output with visual indicators**

## Expected Output

**Up-to-date database:**
```
✅ Database Status: Up-to-date

Component: manager
Current Revision: abc123def456 (Add session status field)
Head Revision:    abc123def456 (Add session status field)

Status: Database schema is current. No migrations pending.
```

**Behind database (migrations pending):**
```
⚠️ Database Status: Behind

Component: manager
Current Revision: abc123def456 (Add session status field)
Head Revision:    def456ghi789 (Add domain fair share)

Status: 2 migrations pending.

Recommended action:
Run /db-migrate to apply pending migrations.
```

**Diverged heads:**
```
🔀 Database Status: Diverged

Component: manager
Current Revision: abc123def456
Head Revisions:   def456ghi789, ghi789jkl012

Status: Multiple migration heads detected.

Recommended action:
Run /db-rebase to resolve diverged heads.
```

## Error Handling

**Database connection failed:**
```
❌ Error: Cannot connect to database

Details: Connection refused to localhost:5432

Recommended actions:
1. Check PostgreSQL is running: docker ps | grep postgres
2. Verify database configuration in config file
3. For halfstack setup: ./scripts/run-dev.sh

Related skill: /cli-executor
```

**Multiple heads detected:**
```
🔀 Error: Multiple heads detected

Details: Found 2 diverged migration heads

Recommended actions:
1. Review migration history: ./py -m alembic history --verbose
2. Resolve heads: /db-rebase
3. Verify result: /db-status

Related skill: /db-rebase
```

**No revision found (fresh database):**
```
❓ Database Status: No revision

Component: manager
Current Revision: None
Head Revision:    abc123def456

Status: Database not initialized.

Recommended action:
Run /db-migrate for initial database setup.
```

## Related Skills

- `/db-migrate` - Apply pending migrations
- `/db-rebase` - Resolve diverged migration heads
- `/cli-executor` - Run component health checks

## Examples

**Check default (manager) database:**
```
User: "Check database migration status"
Agent: /db-status
```

**Check specific component:**
```
User: "Show appproxy database status"
Agent: /db-status --component=appproxy
```

**Check all components:**
```
User: "Check all database statuses"
Agent: [Runs /db-status for manager, accountmgr, and appproxy]
```

## Implementation Notes

- Uses `./backend.ai mgr dbschema show` command internally
- Parses output to extract revision information
- Provides actionable recommendations based on status
- Links to related skills for next steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lablup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
