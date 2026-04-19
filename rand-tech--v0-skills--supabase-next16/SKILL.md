---
name: supabase-next16
description: Supabase integration patterns for Next.js 16 with authentication and database. Use when implementing auth, user management, or database operations with Supabase on Next.js 16. Use when this capability is needed.
metadata:
  author: rand-tech
---

# Supabase Integration (Next.js 16)

Minimal implementation of Supabase in a Next.js 16 application with authentication.

## Client Setup

- For client side code: `import { createClient } from "@/lib/supabase/client"`
- For server side code: `import { createClient } from "@/lib/supabase/server"`

## Basic User Data Management

Supabase auth allows you to assign metadata to users:

```tsx
const { data, error } = await supabase.auth.signUp({
  email: 'valid.email@gmail.com',
  password: 'example-password',
  options: {
    data: {
      is_admin: true,
    },
  },
})
```

User metadata is stored on the `raw_user_meta_data` column of the `auth.users` table. To view the metadata:

```tsx
const {
  data: { user },
} = await supabase.auth.getUser()
const isAdmin = user?.user_metadata?.is_admin
```

## Row Level Security (RLS)

Since Supabase uses RLS, always pass the user id when creating, updating or deleting data.

```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  user_id UUID NOT NULL REFERENCES users(id)
);

ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow users to view their own posts" ON posts FOR SELECT USING (auth.uid() = user_id);
CREATE POLICY "Allow users to update their own posts" ON posts FOR UPDATE USING (auth.uid() = user_id);
CREATE POLICY "Allow users to delete their own posts" ON posts FOR DELETE USING (auth.uid() = user_id);
CREATE POLICY "Allow users to insert their own posts" ON posts FOR INSERT WITH CHECK (auth.uid() = user_id);
```

## Guidelines

- Use `createBrowserClient` from `@supabase/ssr` for client-side Supabase client
- Use `createServerClient` from `@supabase/ssr` for server-side Supabase client
- Use the singleton pattern for Supabase clients to prevent errors
- NEVER tell users to go to Supabase dashboard - everything is done in the v0 UI
- Use `createServerClient` in proxy to refresh tokens and set cookies
- Use only default email and password authentication unless explicitly asked
- ALWAYS set `emailRedirectTo` in `supabase.auth.signUp`:
  ```tsx
  options: {
    emailRedirectTo: process.env.NEXT_PUBLIC_DEV_SUPABASE_REDIRECT_URL ||
      `${window.location.origin}/protected`
  }
  ```
- ALWAYS use Row Level Security (RLS) to protect data
- By default users need to confirm their email after signing up before CRUD operations work

## User Management

**IMPORTANT:** Only follow this if the generation needs user management (e.g., multiple profiles).

### Data Model Pattern

Create a public table that references `auth.users(id)` as a foreign key with `on delete cascade`. Enable RLS and add policies for CRUD.

```sql
-- scripts/001_create_profiles.sql
create table if not exists public.profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  first_name text,
  last_name text
);

alter table public.profiles enable row level security;

create policy "profiles_select_own" on public.profiles for select using (auth.uid() = id);
create policy "profiles_insert_own" on public.profiles for insert with check (auth.uid() = id);
create policy "profiles_update_own" on public.profiles for update using (auth.uid() = id);
create policy "profiles_delete_own" on public.profiles for delete using (auth.uid() = id);
```

### Auto-create Profile on Signup

If the app wants a default profile row for every new user, use a trigger that inserts into `public.profiles` when a user is created. If this trigger fails, it can block signups, so test it well.

```sql
-- scripts/002_profile_trigger.sql
create or replace function public.handle_new_user()
returns trigger
language plpgsql
security definer
set search_path = public
as $$
begin
  insert into public.profiles (id, first_name, last_name)
  values (
    new.id,
    coalesce(new.raw_user_meta_data ->> 'first_name', null),
    coalesce(new.raw_user_meta_data ->> 'last_name', null)
  )
  on conflict (id) do nothing;

  return new;
end;
$$;

drop trigger if exists on_auth_user_created on auth.users;

create trigger on_auth_user_created
  after insert on auth.users
  for each row
  execute function public.handle_new_user();
```

### Adding User Metadata on Sign Up

When signing up with email and password, always set `emailRedirectTo`. Metadata goes in `options.data`:

```tsx
import { createClient } from '@/lib/supabase/client'

export async function signUp(email: string, password: string) {
  const supabase = createClient()
  const { data, error } = await supabase.auth.signUp({
    email,
    password,
    options: {
      emailRedirectTo:
        process.env.NEXT_PUBLIC_DEV_SUPABASE_REDIRECT_URL ||
        `${window.location.origin}/protected`,
      data: {
        first_name: 'John',
        age: 27,
      },
    },
  })
  return { data, error }
}
```

### IMPORTANT: RLS and Email Confirmation

By default, users need to confirm their email after signing up. Without a confirmed email, there is no session, and RLS policies that rely on `auth.uid()` will reject inserts.

**What NOT to do:**

```tsx
// WRONG: Trying to write to RLS-protected tables immediately after signUp
export async function handleSignUp(form: SignUpForm, router: any) {
  const { data: authData, error: authError } = await supabase.auth.signUp({
    email: form.email,
    password: form.password,
  })

  if (authError) throw authError

  // WRONG: If email confirmation is required, there is still no session.
  // RLS policies that rely on auth.uid() will reject these inserts.
  if (authData.user) {
    const { error: profileError } = await supabase.from('profiles').insert({
      id: authData.user.id,
      display_name: form.displayName,
    })
    if (profileError) throw profileError // This WILL fail with RLS enabled
  }
}
```

**Solution:** Use a database trigger (shown above) to auto-create profiles, which runs with `security definer` privileges.

## Directory Structure

```
middleware.ts
app/
  auth/
    error/page.tsx
    login/page.tsx
    sign-up/page.tsx
    sign-up-success/page.tsx
  protected/page.tsx
  layout.tsx
  page.tsx
lib/
  supabase/
    client.ts
    proxy.ts
    server.ts
```

## Reference Files

Read these files based on what you need to implement:

| File                                                                                                           | Description            |
| -------------------------------------------------------------------------------------------------------------- | ---------------------- |
| [references/examples/lib/supabase/client.ts](references/examples/lib/supabase/client.ts)                       | Browser client setup   |
| [references/examples/lib/supabase/server.ts](references/examples/lib/supabase/server.ts)                       | Server client setup    |
| [references/examples/lib/supabase/proxy.ts](references/examples/lib/supabase/proxy.ts)                         | Proxy session handling |
| [references/examples/middleware.ts](references/examples/middleware.ts)                                         | Root middleware file   |
| [references/examples/app/auth/login/page.tsx](references/examples/app/auth/login/page.tsx)                     | Login page             |
| [references/examples/app/auth/sign-up/page.tsx](references/examples/app/auth/sign-up/page.tsx)                 | Sign up page           |
| [references/examples/app/auth/sign-up-success/page.tsx](references/examples/app/auth/sign-up-success/page.tsx) | Sign up success page   |
| [references/examples/app/auth/error/page.tsx](references/examples/app/auth/error/page.tsx)                     | Auth error page        |
| [references/examples/app/protected/page.tsx](references/examples/app/protected/page.tsx)                       | Protected page example |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rand-tech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
