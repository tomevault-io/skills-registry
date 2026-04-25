---
name: error-fixer
description: | Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Error Fixer

Fetch and fix errors flagged for AI correction in the admin dashboard. Supports both JS errors (`error_reports`) and HTTP errors (`http_error_logs`).

**Skill-specific learnings:** See [references/learnings.md](references/learnings.md) for PostgreSQL error codes, RPC debugging patterns, and gotchas.

**Cross-cutting learnings:** See `.claude/LEARNINGS.md` for:
- "Supabase/Database" → RLS+GRANT 403 errors, RPC 404 patterns, CHECK constraint values
- "React/TypeScript" → useEffect infinite loops, null checks

## Workflow

```
1. FETCH    → Get AI-flagged errors from both tables
2. ANALYZE  → Understand each error (stack trace, context, URL, status)
3. FIX      → Apply fixes using systematic debugging
4. MARK     → Mark errors as ai_fixed with notes
5. REPORT   → Summarize what was fixed
6. LEARN    → If non-obvious pattern discovered, invoke /opi to capture it
```

## Step 1: Fetch Flagged Errors

Query both error tables using Supabase MCP:

**JS Errors:**
```sql
SELECT id, error_type, error_message, stack_trace, context, user_action, ai_prompt, created_at
FROM error_reports WHERE ai_status = 'flagged_for_ai'
```

**HTTP Errors:**
```sql
SELECT id, method, url, status_code, response_body, request_context, navigation_path, ai_prompt, created_at
FROM http_error_logs WHERE ai_status = 'flagged_for_ai'
```

Or use RPC functions: `get_errors_for_ai()` and `get_http_errors_for_ai()`.

**Important:** The `ai_prompt` field contains specific instructions from the admin.

## Step 2: Analyze Each Error

### JS Errors
1. **Read the ai_prompt** - Admin's instruction
2. **Parse stack trace** - File path, line number, call chain
3. **Check context** - `route`, `action`, other data
4. **Read affected file(s)**

### HTTP Errors
1. **Read the ai_prompt** - Admin's instruction
2. **Check URL and status code** - What endpoint failed and why
3. **Review response_body** - Error message from server
4. **Check navigation_path** - User's journey to this error
5. **Find the code** - Locate fetch/API call that made this request

## Step 3: Route to the Right Skill and Fix

**DO NOT GUESS.** Route to the appropriate skill based on the error type, then apply the fix.

### Routing table

| Error pattern | Invoke skill | Why |
|---------------|-------------|-----|
| RLS / 403 / permission denied | `security-auditor` | RLS policy or GRANT issue |
| Auth / 401 / JWT / session | `auth-shield` | Authentication flow issue |
| Edge Function 500 / CORS | `edge-function-generator` | Server-side code issue |
| TypeScript / type mismatch / `as any` | `supabase-typing-architect` | Type system issue |
| React render error / UI crash | `frontend-design` | Component issue |
| Lint / format error | `lint-fixer` | Code style issue |
| Slow query / timeout / N+1 | `performance-auditor` | Performance issue |
| Admin panel issue | `admin-panel-builder` | Admin-specific issue |
| Idea Machina error | `idea-machina` | IdeaMachina-specific issue |
| Practice / gamification error | `practice-gamification` | Practice-specific issue |
| General bug / TypeError / undefined | `systematic-debugging` | General debugging |

**Multiple skills may apply.** For example, a 403 on an RPC may need `security-auditor` (fix RLS) + `supabase-typing-architect` (fix types).

After routing, follow the skill's workflow, then return here for Step 4 (marking fixed).

### Common patterns for quick reference

| Error Type | Common Fix |
|------------|------------|
| `TypeError: Cannot read property 'x' of undefined` | Add null checks, optional chaining |
| `TypeError: x is not a function` | Check imports, verify function exists |
| `Rendered more hooks than during the previous render` | Hooks called after early return — move all hooks above early returns |
| `ChunkLoadError` | Code splitting issue, check lazy imports |
| 400 + `23514` check_violation | Read RPC source: `SELECT prosrc FROM pg_proc WHERE proname = 'X'`, find the failing INSERT/UPDATE |
| 400 + `42703` undefined_column | RPC references non-existent column — check table schema, fix RPC |
| 400 + `42883` undefined_function | RPC calls non-existent function — check function name/args |
| 400 Bad Request (other) | Validate request params, check payload format |
| 403 + `42501` insufficient_privilege | Check schema, GRANT for authenticated, AND RLS policy (need all three) |
| 401/403 Unauthorized | Check auth token, verify RLS policies |
| 404 Not Found | Verify endpoint exists, check URL construction |
| 500 Server Error | Check Edge Function logs, fix server-side code |

## Step 4: Mark as Fixed

**Use direct UPDATE for both tables** (the `mark_error_ai_fixed` RPC requires admin role which MCP SQL lacks):

```sql
-- JS Errors
UPDATE error_reports SET ai_status = 'ai_fixed', ai_fixed_at = NOW(),
  ai_fix_notes = 'description' WHERE id = 'error-uuid'

-- HTTP Errors
UPDATE http_error_logs SET ai_status = 'ai_fixed', ai_fixed_at = NOW(),
  ai_fix_notes = 'description' WHERE id = 'error-uuid'
```

**Good fix notes examples:**
- "Added null check for user object in ProfileCard.tsx:45"
- "Fixed RLS policy for authenticated users on topics table"
- "Added missing CORS header to edge function"

## Step 5: Report Summary

```
## Error Fix Summary

### JS Errors
#### Fixed (X)
- [error_type] in file.tsx:line - Brief description

#### Could Not Fix (X)
- [error_type] - Reason

### HTTP Errors
#### Fixed (X)
- [status_code] [method] /path - Brief description

#### Could Not Fix (X)
- [status_code] [method] /path - Reason
```

## Step 6: Learn (invoke /opi)

After fixing errors, evaluate whether any fix revealed a non-obvious pattern worth capturing. Invoke the `opi` skill when:

- The root cause was surprising or non-obvious (e.g., table in wrong schema, MCP role limitations)
- The same mistake could easily happen again in new code
- A workaround was needed for a platform limitation
- The fix involved a multi-step diagnostic that future sessions should skip

**How:** Use the Skill tool to invoke `opi` with a summary of what was learned. The opi skill routes learnings to the right file (global LEARNINGS.md or skill-specific learnings.md).

Skip this step for straightforward fixes (typos, missing null checks, obvious import errors).

## Database Schema Reference

```sql
-- error_reports (JS errors)
ai_status: 'pending' | 'flagged_for_ai' | 'ai_fixed' | 'verified'
ai_prompt: text
ai_fixed_at: timestamp
ai_fix_notes: text

-- http_error_logs (HTTP errors)
ai_status: 'pending' | 'flagged_for_ai' | 'ai_fixed' | 'verified'
ai_prompt: text
ai_fixed_at: timestamp
ai_fix_notes: text

-- RPC functions
get_errors_for_ai()              -- JS errors flagged for AI
get_http_errors_for_ai()         -- HTTP errors flagged for AI
mark_error_ai_fixed(p_id, p_fix_notes)       -- Mark JS error fixed
-- NOTE: No RPC for HTTP errors — use direct UPDATE on http_error_logs
```

## Best Practices

1. **Always read ai_prompt first** - Admin may have specific instructions
2. **Don't fix what you don't understand** - Mark as "could not fix"
3. **Run build after fixes** - Verify no new errors
4. **Write clear fix notes** - Help admin understand what changed
5. **Group related errors** - Multiple errors may have same root cause
6. **Check Edge Function logs** - For HTTP 500 errors, use Supabase MCP `get_logs`
7. **For 400 RPC errors, always read the RPC source first** - `SELECT prosrc FROM pg_proc WHERE proname = 'X'`
8. **After remote RPC fix, create local migration** - `supabase migration new <name>`, write SQL, `supabase migration repair <ts> --status applied`
9. **Parse the PostgreSQL error code** from response_body — it tells you exactly what went wrong (see [references/learnings.md](references/learnings.md) for code reference)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
