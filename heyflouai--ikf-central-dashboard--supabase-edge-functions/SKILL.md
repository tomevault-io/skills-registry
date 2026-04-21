---
name: supabase-edge-functions
description: Best practices for developing, deploying, and debugging Supabase Edge Functions (Deno runtime). Use when working with Edge Functions for tasks like ingest pipelines, webhooks, scheduled jobs, or database triggers. Covers authentication patterns (service role vs anon key), error debugging, database integration, and common pitfalls. Use when this capability is needed.
metadata:
  author: heyflouai
---

# Supabase Edge Functions Best Practices

Comprehensive guide for Supabase Edge Functions development, debugging, and integration with database triggers.

## Core Concepts

### Authentication Patterns

Edge Functions support two authentication modes:

1. **JWT Verification (verify_jwt: true)** - Default, validates Authorization header contains valid JWT
2. **Custom Auth (verify_jwt: false)** - Function handles auth internally (API keys, webhooks)

**Service Role vs Anon Key:**
- **Anon key** (`eyJ...`, ~219 chars): JWT for client-side, limited permissions via RLS
- **Service role key** (`sb_secret_...`, ~600 chars): Full admin access, bypasses RLS, NEVER expose to client

### Database Trigger Integration

When calling Edge Functions from database triggers using `pg_net`:

```sql
SELECT net.http_post(
  url := 'https://project.supabase.co/functions/v1/function-name',
  headers := jsonb_build_object(
    'Content-Type', 'application/json',
    'Authorization', 'Bearer ' || v_service_role_key  -- Must be SERVICE ROLE KEY
  ),
  body := jsonb_build_object('run_id', p_run_id)
);
```

**Critical:** Database triggers MUST use service role key, not anon key.

## Common Issues and Solutions

### Issue 1: Auth Token Mismatch

**Symptoms:**
- Logs show: "Token prefix: eyJ... Expected prefix: sb_secret_..."
- Function returns 401 or auth errors
- Database trigger calls fail silently

**Root Cause:**
Anon key stored instead of service role key in database config.

**Solution:**
1. Get service role key from Dashboard > Settings > API > service_role
2. Verify key starts with `sb_secret_` and is ~600 characters
3. Update database config:
```sql
UPDATE private.config
SET value = 'sb_secret_YOUR_KEY_HERE'
WHERE key = 'service_role_key';
```

**Verification:**
```sql
SELECT
  key,
  LEFT(value, 10) as value_preview,
  LENGTH(value) as key_length
FROM private.config
WHERE key = 'service_role_key';
-- Should show: sb_secret_... with length ~600
```

### Issue 2: Function Deployment Errors

**Common Errors:**
- Import map not found
- Module resolution failures
- Type errors in Deno runtime

**Solutions:**

**Import Maps:**
Use `deno.json` for dependencies:
```json
{
  "imports": {
    "supabase": "jsr:@supabase/supabase-js@2",
    "postgres": "https://deno.land/x/postgres@v0.17.0/mod.ts"
  }
}
```

**Type Safety:**
Import runtime types at top of function:
```typescript
import "jsr:@supabase/functions-js/edge-runtime.d.ts";
```

**Testing Locally:**
```bash
# Install Supabase CLI
supabase functions serve function-name --env-file .env.local

# Test with curl
curl -i --location --request POST 'http://localhost:54321/functions/v1/function-name' \
  --header 'Authorization: Bearer YOUR_ANON_KEY' \
  --header 'Content-Type: application/json' \
  --data '{"run_id":"test-uuid"}'
```

### Issue 3: Database Connection from Edge Functions

**Pattern:**
```typescript
import { createClient } from "jsr:@supabase/supabase-js@2";

Deno.serve(async (req: Request) => {
  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!  // Service role for admin ops
  );

  // Now can bypass RLS for admin operations
  const { data, error } = await supabase
    .from('forecast_runs')
    .update({ status: 'processing' })
    .eq('id', runId);
});
```

**Environment Variables:**
Edge Functions automatically have access to:
- `SUPABASE_URL` - Project URL
- `SUPABASE_ANON_KEY` - Public anon key
- `SUPABASE_SERVICE_ROLE_KEY` - Admin service role key

### Issue 4: Debugging Failed Triggers

**Check trigger configuration:**
```sql
SELECT * FROM private.config WHERE key IN ('supabase_url', 'service_role_key');
```

**Check pg_net requests:**
```sql
SELECT * FROM net._http_response ORDER BY created DESC LIMIT 10;
```

**Check Edge Function logs:**
Use Supabase MCP tool `get_logs` or Dashboard > Edge Functions > Logs

**Enable verbose logging in function:**
```typescript
console.log('[function-name] Processing run_id:', runId);
console.log('[function-name] Request headers:', Object.fromEntries(req.headers));
console.log('[function-name] Environment check:', {
  hasUrl: !!Deno.env.get('SUPABASE_URL'),
  hasServiceKey: !!Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')
});
```

## Deployment Workflow

### 1. Develop Locally
```bash
supabase functions serve function-name
```

### 2. Test Locally
```bash
curl -i --location --request POST 'http://localhost:54321/functions/v1/function-name' \
  --header 'Authorization: Bearer ANON_KEY' \
  --data '{"test": "data"}'
```

### 3. Deploy to Production
```bash
supabase functions deploy function-name
```

### 4. Verify Deployment
```bash
# Check function exists
supabase functions list

# Test production endpoint
curl -i --location --request POST 'https://PROJECT.supabase.co/functions/v1/function-name' \
  --header 'Authorization: Bearer ANON_KEY' \
  --data '{"test": "data"}'
```

### 5. Monitor Logs
```bash
# Via CLI
supabase functions logs function-name

# Via Dashboard
Dashboard > Edge Functions > function-name > Logs
```

## File Structure Best Practices

```
supabase/functions/
├── function-name/
│   ├── index.ts              # Main handler
│   ├── deno.json             # Import map
│   ├── parsers/              # Domain logic (separate from handler)
│   │   └── csv-parser.ts
│   └── _shared/              # Shared utilities (symlinked)
│       └── validation.ts
```

**Shared Code:**
Use `_shared/` directory for code reused across functions. Supabase CLI automatically includes it.

## Error Handling Pattern

```typescript
import "jsr:@supabase/functions-js/edge-runtime.d.ts";

Deno.serve(async (req: Request) => {
  try {
    // Parse request
    const body = await req.json();

    // Validate input
    if (!body.run_id) {
      return new Response(
        JSON.stringify({ error: 'run_id required' }),
        { status: 400, headers: { 'Content-Type': 'application/json' } }
      );
    }

    // Process
    const result = await processData(body.run_id);

    // Success response
    return new Response(
      JSON.stringify({ success: true, data: result }),
      { status: 200, headers: { 'Content-Type': 'application/json' } }
    );

  } catch (error) {
    console.error('[function-name] Error:', error);

    return new Response(
      JSON.stringify({
        error: error.message,
        stack: error.stack  // Include for debugging, remove in production
      }),
      { status: 500, headers: { 'Content-Type': 'application/json' } }
    );
  }
});
```

## Security Best Practices

1. **Never expose service role key to client** - Only use in Edge Functions or database
2. **Use RLS policies** - Even with service role, validate permissions in function logic
3. **Validate all inputs** - Never trust request data
4. **Rate limiting** - Implement for public endpoints
5. **CORS configuration** - Restrict origins in production
6. **Secrets management** - Use Supabase secrets, not hardcoded values

## References

- [Supabase Edge Functions Docs](https://supabase.com/docs/guides/functions)
- [Deno Runtime API](https://deno.land/api)
- [pg_net Extension](https://supabase.com/docs/guides/database/extensions/pg_net)
- [Supabase MCP Tools](https://github.com/supabase/mcp)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heyflouai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
