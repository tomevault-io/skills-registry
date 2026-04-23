---
name: frontend-realtime-sync
description: Use when adding new tables to realtime events system, implementing realtime subscriptions, or debugging org-scoped table sync issues
metadata:
  author: ntq78
---

# Frontend Realtime Sync

Organization-scoped realtime synchronization pattern that automatically invalidates TanStack Query caches when database changes occur.

## Core Concepts

### Realtime Table Events System

Organization-scoped notification system that eliminates need for `organization_id` on junction tables:

- **Backend:** Triggers insert events into `realtime_table_events` table
- **Frontend:** Subscribes to `realtime_table_events` filtered by organization
- **Event payload:** `{ table_name, event_type, organization_id }`
- **No hardcoded table list** - works for any table with backend trigger

### Channel Pattern

- Separate channel per organization: `org:org_123`
- Subscribes to `realtime_table_events` table with filter: `organization_id=eq.{orgId}`
- Automatically resubscribes when user's organization memberships change

### Query Invalidation (Granular Record-Level)

- Backend trigger inserts notification into `realtime_table_events` with `table_name` and `record_id`
- Frontend receives INSERT event with both `table_name` and `record_id` in payload
- Invalidation logic:
    - `.list()` queries: ALWAYS invalidate on any table change
    - `.record(id)` queries: ONLY invalidate when `record_id` matches
- TanStack Query automatically re-fetches affected queries
- UI updates in real-time across all users in the organization

**Performance benefit:** With 500 concurrent users updating different records, only affected queries refetch instead of all table queries.

## Architecture

```
Database Change (Postgres)
    |
    v
Trigger: get_organization_id_for_change()
(Traces change to affected organization + extract record ID)
    |
    v
INSERT into realtime_table_events
(table_name, event_type, organization_id, record_id)
    |
    v
Supabase Realtime Publication
    |
    v
Organization Channel (org:org_123)
    |
    v
useSupabaseRealtimeSync Hook
    |
    v
Extract table_name and record_id from payload
    |
    v
Query Invalidation (granular predicate):
  - .list() queries: Always invalidate
  - .record(id) queries: Only if record_id matches
    |
    v
UI Re-fetch & Update (only affected queries)
```

## Adding New Tables to Realtime

**Frontend: No changes needed!** Just ensure QueryKeys use `.list()` for list queries or `.record(id)` for single-record queries.

**Backend only (in migration):**

1. **Create your table (no organization_id required for junction tables)**

    ```sql
    -- Example: Junction table without organization_id
    CREATE TABLE my_junction_table (
      parent_id uuid REFERENCES parent_table(id),
      child_id uuid REFERENCES child_table(id),
      PRIMARY KEY (parent_id, child_id)
    );
    ```

2. **Update get_organization_id_for_change() function:**

    ```sql
    CREATE OR REPLACE FUNCTION public.get_organization_id_for_change()
    RETURNS TEXT AS $$
    DECLARE
      org_id TEXT;
      record_data JSONB;
    BEGIN
      IF TG_OP = 'DELETE' THEN
        record_data := to_jsonb(OLD);
      ELSE
        record_data := to_jsonb(NEW);
      END IF;

      CASE TG_TABLE_NAME
        -- Add your new table:
        WHEN 'my_junction_table' THEN
          SELECT p.organization_id INTO org_id
          FROM parent_table p
          WHERE p.id = (record_data->>'parent_id');

        -- ... other tables ...
      END CASE;

      RETURN org_id;
    END;
    $$ LANGUAGE plpgsql SECURITY DEFINER SET search_path = public;
    ```

3. **Attach trigger:**

    ```sql
    DROP TRIGGER IF EXISTS trigger_notify_org_change ON my_junction_table;
    CREATE TRIGGER trigger_notify_org_change
      AFTER INSERT OR UPDATE OR DELETE ON my_junction_table
      FOR EACH ROW
      EXECUTE FUNCTION notify_organization_of_table_change();
    ```

4. **Verify QueryKeys exist (usually already present):**
    ```typescript
    // src/utils/query/queryKeys.ts
    export const QueryKeys = {
        my_junction_table: createTableFactory("my_junction_table"),
    };
    ```

**That's it!** Frontend automatically invalidates queries when it receives `table_name: "my_junction_table"` in event payload.

## Global Tables (No Realtime)

Tables WITHOUT `organization_id` should use manual invalidation:

- `organizations` - Use manual invalidation in mutation hooks
- `profiles` - Use manual invalidation in mutation hooks
- `role_permissions` - Use manual invalidation in mutation hooks

Why no realtime for global tables?

- No way to scope by organization
- Would broadcast to all users (privacy/performance issue)
- Manual invalidation provides instant UI feedback for mutations

## Provider Setup

```typescript
// App.tsx
<Provider_Query>
  <Provider_ANTD>
    <Provider_SupabaseRealtimeSync>
      <RouterProvider router={router} />
    </Provider_SupabaseRealtimeSync>
  </Provider_ANTD>
</Provider_Query>
```

The provider should be:

- After `QueryClientProvider` (needs query client context)
- Inside authentication providers (needs user context)
- Before router/app components

## Realtime Flow for Org-Scoped Tables

1. User A creates a project in organization `org_123`
2. Postgres INSERT trigger fires on `projects` table
3. Trigger calls `get_organization_id_for_change()` → returns `org_123`
4. Trigger inserts into `realtime_table_events`: `{organization_id: 'org_123', table_name: 'projects', event_type: 'INSERT'}`
5. Supabase Realtime broadcasts INSERT event to channel `org:org_123`
6. All users subscribed to `org:org_123` receive notification event
7. Frontend extracts `table_name: 'projects'` from payload
8. `handleTableChange` invalidates all queries with `'projects'` in query keys
9. TanStack Query re-fetches affected queries
10. UI updates automatically for all users in the organization

**Key Advantage:** Works even for junction tables without `organization_id` column, as backend traces relationship to affected organization.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
