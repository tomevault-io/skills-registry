---
name: add-db-table-rls
description: Create/modify a Supabase table with RLS policies, indexes, and safe access patterns. Use when this capability is needed.
metadata:
  author: tahmidbintaslim
---

# Skill Instructions

1. Define schema (columns, constraints, timestamps)
2. Enable RLS
3. Add policies (select/insert/update/delete scoped to auth.uid())
4. Add indexes for common queries (user_id + date)
5. Update `docs/db.md`
6. Ensure server queries scope by session user
7. Run `npm run lint` and tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tahmidbintaslim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
