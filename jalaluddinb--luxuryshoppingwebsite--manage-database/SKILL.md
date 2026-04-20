---
name: manage-database
description: Manage application databases including local development and remote production environments. Use when this capability is needed.
metadata:
  author: jalaluddinb
---

# Database Management Skill

This skill details how to manage the application's database, including switching between local and remote environments and handling migrations.

## Database Modes

### 1. Local Development (SQLite)
- **Setup**: Set `USE_LOCAL_DB=true` in `.env`.
- **File**: `instance/luxury.db`.
- **Use Case**: Fast iteration, offline development.

### 2. Remote Production (Azure SQL)
- **Setup**: Set `USE_LOCAL_DB=false` (or remove from `.env`).
- **Config**: Requires `DATABASE_URL` with valid Azure SQL credentials.
- **Use Case**: Integration testing, production deployment.

## Common Tasks

### 1. Reset Database
- **Action**: Delete `instance/luxury.db` and restart the app.
- **Note**: `app.py` automatically calls `db.create_all()` on startup if tables are missing.
- **Warning**: This erases all local data (users, products, orders).

### 2. Schema Changes
- **Detect**: SQLAlchemy warnings (e.g., overlapping relationships).
- **Fix**: Update models in `app.py`.
- **Apply**: For SQLite, drop/recreate is easiest. For Azure, use migration scripts (not yet automated).

## Relevant Files
- `app.py` (Model definitions: `User`, `Product`, `Order`, `OrderItem`)
- `.env` (Database configuration toggle)
- `instance/luxury.db` (Local database file)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jalaluddinb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
