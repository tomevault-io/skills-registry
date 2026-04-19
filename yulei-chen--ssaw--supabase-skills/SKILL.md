---
name: supabase-skills
description: Use Supabase client, RLS, and storage correctly in Nuxt apps. Use when working with Supabase auth, database queries, Row Level Security, storage buckets, or @nuxtjs/supabase. Use when this capability is needed.
metadata:
  author: yulei-chen
---

# Supabase Skills

## Client in Nuxt

- **Client**: `useSupabaseClient()` in composables or pages (with `@nuxtjs/supabase`). Use for database and storage.
- **User**: `useSupabaseUser()` for current auth user; use in middleware or setup for redirects.

## Database

- **Queries**: Chain `.from('table')`, `.select('*')` or `.select('*, relation(*)')`, `.eq()`, `.order()`, etc. Prefer explicit `.select()` with needed columns/relations.
- **Joins**: Use Postgres relation syntax in select, e.g. `.select('*, block_notes(*, block_note_attachments(*))')`.
- **Errors**: Check `error` from `{ data, error }`; surface in UI or log as appropriate.
- **Types**: Cast results when needed, e.g. `(data ?? []) as TimeBlockWithNote[]`.

## RLS (Row Level Security)

- **Enable RLS** on all public tables: `alter table public.<table> enable row level security;`
- **Policies**: One policy per operation (select/insert/update/delete) or `for all` when same condition applies. Use `using (...)` for read/update/delete and `with check (...)` for insert/update.
- **Auth**: Use `auth.uid()` in policy expressions. For “own row” use `auth.uid() = user_id` (or equivalent column).
- **Partner/shared access**: Use `exists (select 1 from public.profiles me, public.profiles other where me.id = auth.uid() and ...)` to enforce mutual partner relationship before granting read.

## Storage

- **Buckets**: Create buckets in Supabase dashboard (e.g. `avatars`, `block-images`). Prefer path structure like `{user_id}/...` or `avatars/{user_id}/...` so policies can use `(storage.foldername(name))[1]` or `[2]` for `user_id`.
- **Policies**: Attach policies to `storage.objects` with `bucket_id = 'bucket-name'` and path checks via `storage.foldername(name)`. Use `auth.uid()::text` when comparing to path segments.
- **Public read**: For public buckets, read is allowed without a select policy; for private buckets, add select policies for owner (and partner if applicable).

## Schema and triggers

- **Profiles**: Common pattern is a `public.profiles` table with `id uuid primary key references auth.users(id) on delete cascade` and a trigger on `auth.users` (e.g. `handle_new_user`) to insert a row into `profiles` on signup.
- **Foreign keys**: Use `references public.<table>(id) on delete cascade` for dependent tables so deletes propagate.

## Project patterns (this repo)

- **Composables**: Fetch in composables with `useSupabaseClient()` and `useAsyncData`; key includes user/date (or other reactive params); `watch` for reactivity.
- **Schema**: Tables in `supabase/schema.sql`; storage policies in `supabase/storage-policies.sql`. Run in Supabase SQL editor after creating buckets.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yulei-chen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
