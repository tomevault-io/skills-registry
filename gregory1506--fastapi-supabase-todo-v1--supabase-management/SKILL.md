---
name: supabase-management
description: Tools and workflows for managing the Supabase backend in the FastAPI Todo App. Use when this capability is needed.
metadata:
  author: gregory1506
---

# Supabase Management Skill

This skill provides workflows and knowledge for managing the Supabase backend specific to this project (`fastapi-supabase-todo-v1`).

## 1. Health Checks

Do NOT rely on the standard `/health` endpoint for database connectivity. It is a service logic-only check.

**To check database connectivity:**
- Use the **`/db-health`** endpoint logic.
- The project includes a dedicated script for this: `scripts/check_db.py`.
- **Usage**: `python scripts/check_db.py`
    - This script attempts to verify connectivity and the existence of the `todos` table.

## 2. Troubleshooting Connection Issues

If you encounter `503 Service Unavailable` or `PGRST301` errors:

1.  **Check Environment Variables**:
    - Ensure `SUPABASE_URL` and `SUPABASE_KEY` (Anon Key) are set.
    - For admin operations (like `/db-health`), `SUPABASE_SERVICE_KEY` is required.

2.  **Refer to Documentation**:
    - See `docs/TROUBLESHOOTING_SUPABASE.md` for specific error codes and fixes.
    - Common issue: Supabase project is paused (Free tier limit). Solution: Log in to Supabase dashboard to unpause.

## 3. Database Schema

-   **Schema File**: `schema.sql` (or `schema_with_auth.sql` for auth policies).
-   **Applying Changes**: This project does NOT use an automated migration tool (like Alembic) yet. Changes must be applied via the Supabase SQL Editor.

## 4. Authentication & RLS

-   **RLS Policies**: Row Level Security is ENABLED on the `todos` table.
-   **Service Key**: To bypass RLS (e.g., for health checks or admin tasks), use the `SUPABASE_SERVICE_KEY` and the `get_admin_client()` function in `app/db/supabase.py`.
-   **Anon Key**: Used for standard user interactions; respects RLS policies.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gregory1506) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
