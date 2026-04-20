---
name: supabase-backend
description: Interactions with Supabase backend. Use when fetching data, updating records, or managing auth. Use when this capability is needed.
metadata:
  author: mhmalvi
---

# Supabase Backend Skill

When interacting with Supabase:

## Client Usage

- Import the typed client from `src/integrations/supabase/client.ts`.
  ```typescript
  import { supabase } from "@/integrations/supabase/client";
  ```

## Type Safety

- Always leverage the generated Database types.
- Queries should automatically be typed if the client is typed correctly.
  ```typescript
  const { data, error } = await supabase
    .from('jobs')
    .select('*');
  // data is typed as Job[]
  ```

## Error Handling

- Always check for `error` after a request.
  ```typescript
  if (error) {
    console.error('Error fetching jobs:', error);
    toast.error('Failed to load jobs');
    return;
  }
  ```

## Auth

- Use `supabase.auth.getUser()` to get the current user server-side or in protected routes.
- Use `useAuth` hook (if available) or listen to `onAuthStateChange` for client-side auth state.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhmalvi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
