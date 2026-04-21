---
name: syntra-functions
description: Edge functions and scheduled jobs with Syntra. Use when creating serverless functions, deploying code to the edge runtime, invoking functions by slug, setting up cron jobs, or managing scheduled tasks. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra Functions & Schedules

## Edge functions

Syntra runs edge functions on a Deno runtime. Write TypeScript/JavaScript functions that deploy and execute on demand.

### Create and deploy

1. `functions_create` — create a function with source code
2. `functions_deploy` — deploy to the edge runtime
3. `functions_invoke` — call by slug

### Function lifecycle

```
functions_create → functions_deploy → functions_invoke
                         ↑
                  functions_update → functions_deploy
```

### Create a function

```json
{
  "name": "Hello World",
  "slug": "hello-world",
  "code": "export default async function handler(req: Request): Promise<Response> {\n  const body = await req.json().catch(() => ({}));\n  return new Response(JSON.stringify({ message: 'Hello!', input: body }), {\n    headers: { 'Content-Type': 'application/json' }\n  });\n}",
  "description": "A simple hello world function"
}
```

### Function management

- `functions_list` — list all functions with optional `status` filter and pagination
- `functions_get` / `functions_get_by_slug` — get function details
- `functions_update` — update code, name, slug, description, or status
- `functions_delete` — delete a function
- `functions_list_deployments` — view deployment history

### Invoke a function

```json
{
  "slug": "hello-world",
  "body": { "name": "Claude" },
  "headers": { "X-Custom-Header": "value" }
}
```

## Scheduled jobs (cron)

Run functions on a schedule using cron expressions.

### Create a scheduled job

```json
{
  "name": "Daily cleanup",
  "cron_schedule": "0 3 * * *",
  "function_url": "http://localhost:7133/functions/cleanup",
  "http_method": "POST",
  "body": { "older_than_days": 30 },
  "is_active": true
}
```

### Cron expressions

| Expression | Schedule |
|---|---|
| `* * * * *` | Every minute |
| `0 * * * *` | Every hour |
| `0 0 * * *` | Daily at midnight |
| `0 3 * * *` | Daily at 3 AM |
| `0 0 * * 1` | Every Monday |
| `0 0 1 * *` | First of each month |
| `*/15 * * * *` | Every 15 minutes |

Format: `minute hour day-of-month month day-of-week`

### Job management

- `schedules_list_jobs` — list all jobs
- `schedules_get_job` — get job details
- `schedules_update_job` — update schedule, URL, or body
- `schedules_delete_job` — delete a job
- `schedules_toggle_active` — enable/disable without deleting
- `schedules_list_job_logs` — view execution history with status and response

### Common patterns

**Periodic data cleanup:**
```json
{ "name": "Purge old logs", "cron_schedule": "0 2 * * *", "function_url": "http://localhost:7133/functions/purge-logs", "http_method": "POST" }
```

**Scheduled report:**
```json
{ "name": "Weekly report", "cron_schedule": "0 9 * * 1", "function_url": "http://localhost:7133/functions/send-report", "http_method": "POST" }
```

**Health ping:**
```json
{ "name": "Health check", "cron_schedule": "*/5 * * * *", "function_url": "http://localhost:7130/api/health", "http_method": "GET" }
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
