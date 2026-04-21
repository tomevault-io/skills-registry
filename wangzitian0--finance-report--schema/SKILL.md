---
name: schema
description: PostgreSQL database schema, table structures, relationships, and migration rules. Use this skill when working with SQLAlchemy models, Alembic migrations, or database design. Use when this capability is needed.
metadata:
  author: wangzitian0
---

# Database Schema

> **Core Definition**: PostgreSQL core table structures and relationships.

## Key Tables

- **Users**: User accounts with email/password
- **Accounts**: Chart of accounts (ASSET/LIABILITY/EQUITY/INCOME/EXPENSE)
- **JournalEntries**: Journal entry headers
- **JournalLines**: Journal entry lines (debit/credit)
- **BankStatements**: Imported statement headers
- **BankStatementTransactions**: Statement transactions
- **ReconciliationMatches**: Match records
- **ChatSessions/ChatMessages**: AI chat history

## Design Constraints

### Naming Conventions

- **Explicit Enums**: ALWAYS provide `name` parameter to SQLAlchemy `Enum`
  - ❌ Bad: `sa.Column(sa.Enum(Status))`
  - ✅ Good: `sa.Column(sa.Enum(Status, name="journal_entry_status"))`
- **Migration Length**: Keep Alembic migration descriptions < 100 characters

### Async Session Management

1. Use `get_db` FastAPI dependency for routers
2. Routers handle commit/rollback; Services use `flush()`
3. Ensure every session is closed

### Recommended Patterns

- Use `DECIMAL(18,2)` for amounts
- Use `created_at`/`updated_at` audit fields
- Use UUID primary keys

### Prohibited Patterns

- **NEVER** use FLOAT for monetary amounts
- **NEVER** directly delete posted entries, only void

## Index Strategy

```sql
CREATE INDEX idx_accounts_user_id ON accounts(user_id);
CREATE INDEX idx_journal_entries_user_id ON journal_entries(user_id);
CREATE INDEX idx_journal_entries_status ON journal_entries(status);
CREATE INDEX idx_journal_entries_date ON journal_entries(entry_date);
```

## Source Files

- **Models**: `apps/backend/src/models/`
- **Migrations**: `apps/backend/migrations/`
- **Schemas**: `apps/backend/src/schemas/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wangzitian0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
