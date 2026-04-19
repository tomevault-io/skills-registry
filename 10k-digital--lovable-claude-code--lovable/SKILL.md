---
name: lovable
description: | Use when this capability is needed.
metadata:
  author: 10k-digital
---

# Lovable Integration Skill

This skill enables Claude Code to work effectively with Lovable.dev projects while respecting Lovable's deployment requirements.

## When to Use This Skill

Activate when:
- User mentions "Lovable" or "lovable.dev"
- Project has `supabase/` directory with Edge Functions
- User asks to deploy edge functions
- User creates database migrations
- User asks about Lovable Cloud or backend deployment
- Project appears to be a Lovable project (React + Supabase structure)

## Core Concept

Lovable uses **two-way GitHub sync** on the `main` branch only:
- Frontend code syncs automatically
- Backend operations (Edge Functions, migrations, RLS) require Lovable prompts

## What Syncs Automatically (GitHub → Lovable)

✅ Edit freely and push to `main`:
- `src/` - All React components, pages, hooks, utils
- `public/` - Static assets
- Config files - vite.config.ts, tailwind.config.js, tsconfig.json
- `package.json` - Dependencies
- `supabase/functions/*/index.ts` - Edge Function **code** (not deployment)
- `supabase/migrations/*.sql` - Migration **files** (not application)

## What Requires Lovable Deployment

⚠️ After editing, provide Lovable prompt:

| Change Type | Lovable Prompt |
|-------------|----------------|
| Edge Function code | `"Deploy the [name] edge function"` |
| All Edge Functions | `"Deploy all edge functions"` |
| New migration file | `"Apply pending Supabase migrations"` |
| New table needed | `"Create a [name] table with columns: [list]"` |
| RLS policy | `"Enable RLS on [table] allowing [who] to [what]"` |
| Storage bucket | `"Create a [public/private] bucket called [name]"` |
| Secret/env var | Manual: Cloud → Secrets → Add |

## Response Format

When backend deployment is needed, always output:

```
📋 **LOVABLE PROMPT:**
> "[exact prompt to copy-paste]"
```

For destructive operations, add:
```
⚠️ **Warning**: [explanation of risk]
```

## File Structure Reference

```
project/
├── src/                          # ✅ Safe - auto-syncs
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── lib/
│   └── integrations/supabase/
│       ├── client.ts             # ⚠️ Has Supabase URLs
│       └── types.ts
├── supabase/
│   ├── functions/                # ✅ Edit code, ⚠️ needs deploy
│   │   └── [function-name]/
│   │       └── index.ts
│   ├── migrations/               # ✅ Create files, ⚠️ needs apply
│   │   └── YYYYMMDDHHMMSS_*.sql
│   └── config.toml               # ⚠️ Lovable Cloud manages
├── .env                          # Local only - Lovable ignores
└── CLAUDE.md                     # Project context
```

## Backend Types

### Lovable Cloud
- Backend managed entirely by Lovable
- No Supabase dashboard access
- All operations via Lovable prompts
- Secrets in Cloud → Secrets UI

### Own Supabase
- Direct Supabase dashboard access
- Can use Supabase CLI: `supabase functions deploy`
- More flexibility but manual setup

## Quick Prompts Reference

### Edge Functions
```
"Deploy all edge functions"
"Deploy the send-email edge function"
"Create an edge function called [name] that [description]"
"Show logs for [name] edge function"
"The [name] edge function returns [error]. Fix it"
```

### Database
```
"Create a [name] table with columns: id (uuid), name (text), created_at (timestamp)"
"Add a [column] column of type [type] to [table]"
"Add foreign key from [table1].[col] to [table2].id"
"Apply pending Supabase migrations"
```

### RLS Policies
```
"Enable RLS on [table]"
"Add RLS policy on [table] allowing authenticated users to read all rows"
"Add RLS policy on [table] allowing users to only access their own rows"
```

### Storage
```
"Create a public storage bucket called [name]"
"Create a private storage bucket called [name]"
"Allow authenticated users to upload to [bucket]"
```

### Auth
```
"Enable Google authentication"
"Enable GitHub authentication"
"When user signs up, create row in profiles table"
```

## Branch Rules

- **Only `main` syncs** with Lovable
- Feature branches don't deploy until merged
- Lovable syncs within 1-2 minutes of push

## Yolo Mode - Automated Deployments (Beta)

When `yolo_mode: on` in CLAUDE.md, deployments are automated via browser automation:

### How It Works

Instead of showing manual prompts, the **yolo skill** (`/skills/yolo/SKILL.md`) takes over:
1. Automatically navigates to Lovable.dev
2. Submits deployment prompts
3. Monitors for success/failure
4. Runs verification tests (if enabled)
5. Reports deployment summary

### When Yolo Mode Activates

- During `/lovable:deploy-edge` command
- During `/lovable:apply-migration` command
- When `yolo_mode: on` in CLAUDE.md

### Configure Yolo Mode

```
/lovable:yolo on               # Enable with testing
/lovable:yolo on --no-testing  # Enable without testing
/lovable:yolo on --debug       # Enable with verbose logs
/lovable:yolo off              # Disable
```

### Beta Status

⚠️ Yolo mode is in beta:
- Requires Claude in Chrome extension
- May have bugs or UI compatibility issues
- Always has manual fallback
- See `/skills/yolo/SKILL.md` for details

## Debugging Checklist

1. **Frontend not updating?**
   - On `main` branch?
   - Changes pushed?
   - Wait 1-2 min

2. **Edge Function not working?**
   - Deployed via Lovable (or yolo mode)?
   - Secrets set in Cloud UI?
   - Check logs in Lovable

3. **Database query failing?**
   - Migration applied (via Lovable or yolo mode)?
   - RLS policies correct?
   - Table exists?

4. **Yolo mode not working?**
   - Check `yolo_mode: on` in CLAUDE.md
   - Chrome extension installed?
   - Logged into Lovable?
   - See yolo skill for troubleshooting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/10k-digital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
