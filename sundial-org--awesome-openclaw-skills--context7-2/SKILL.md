---
name: context7-2
description: | Use when this capability is needed.
metadata:
  author: sundial-org
---

# Context7 Documentation Fetcher

Retrieve current library documentation via Context7 API.

## Workflow

### 1. Search for the library

```bash
python3 ~/.claude/skills/context7/scripts/context7.py search "<library-name>"
```

Example:
```bash
python3 ~/.claude/skills/context7/scripts/context7.py search "next.js"
```

Returns library metadata including the `id` field needed for step 2.

### 2. Fetch documentation context

```bash
python3 ~/.claude/skills/context7/scripts/context7.py context "<library-id>" "<query>"
```

Example:
```bash
python3 ~/.claude/skills/context7/scripts/context7.py context "/vercel/next.js" "app router middleware"
```

Options:
- `--type txt|md` - Output format (default: txt)
- `--tokens N` - Limit response tokens

## Quick Reference

| Task | Command |
|------|---------|
| Find React docs | `search "react"` |
| Get React hooks info | `context "/facebook/react" "useEffect cleanup"` |
| Find Supabase | `search "supabase"` |
| Get Supabase auth | `context "/supabase/supabase" "authentication row level security"` |

## When to Use

- Before implementing any library-dependent feature
- When unsure about current API signatures
- For library version-specific behavior
- To verify best practices and patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
