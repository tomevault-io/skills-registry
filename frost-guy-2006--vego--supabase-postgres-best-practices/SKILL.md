---
name: supabase-best-practices
description: Official skill for RLS & Edge Functions. Covers Row Level Security policies, database optimization, and secure backend patterns. Use when this capability is needed.
metadata:
  author: frost-guy-2006
---

# Supabase Best Practices

> **Status**: Value placeholder. Original source could not be fetched.
> **Goal**: Secure and optimized Supabase implementation.

## Row Level Security (RLS)
- **Enable RLS**: Always enable RLS on public tables.
- **Policies**: Define strict `SELECT`, `INSERT`, `UPDATE`, `DELETE` policies.
- **Service Role**: Use service role keys only in secure server environments (Edge Functions), never in client app.

## Database Optimization
- **Indexes**: Add indexes on frequently queried columns (esp. foreign keys).
- **Functions**: Use Postgres functions (RPC) for complex logic.
- **Triggers**: Use triggers for automated data integrity (e.g., `updated_at`).

## Edge Functions
- Keep them small and focused (SRP).
- Validate inputs rigorously.
- Handle CORS properly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/frost-guy-2006) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
