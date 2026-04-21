---
name: syntra-status
description: System diagnostics and monitoring for Syntra. Use when checking backend health, viewing system info, inspecting audit logs, reviewing MCP usage statistics, listing modules, or troubleshooting connectivity issues. Use when this capability is needed.
metadata:
  author: mauricioperera
---

# Syntra Status & Diagnostics

## Quick health check

1. `system_get_metadata` — version, uptime, database status, active modules
2. `system_list_modules` — list all backend modules and their status

If `system_get_metadata` fails, the backend is unreachable. Check:
- Is the server running? (`bun run dev` or Docker container)
- Is the MCP URL correct? (default: `http://localhost:7130/api/mcp`)
- Is the API key valid? (`X-API-Key: ik_...`)

## MCP usage statistics

- `usage_get_stats` — aggregated tool usage grouped by tool name (call count, success rate)
- `usage_list_records` — individual usage records with pagination, filterable by `tool_name`

## Audit logs

- `system_list_audit_logs` — list log entries with optional filters:
  - `module`: filter by module (auth, database, storage, ai, etc.)
  - `action`: filter by action type
  - `actor`: filter by who performed the action
  - `limit` / `offset`: pagination
- `system_get_log_stats` — log statistics grouped by module
- `system_create_audit_log` — manually create a log entry

## Deployments

- `system_list_deployments` — list deployment records with status filter
- `system_get_deployment` — get deployment details
- `system_create_deployment` / `system_update_deployment` / `system_delete_deployment` — manage records

Deployment statuses: `pending`, `building`, `deploying`, `active`, `failed`

## Email

- `system_send_email` — send email via configured SMTP provider
  - `to`: single email or array of emails
  - `subject`: email subject
  - `html`: HTML body
  - `text`: optional plain text fallback

Requires `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, and `EMAIL_FROM` environment variables.

## Secrets

- `secrets_list` — list stored secrets (metadata only, not values)

## Troubleshooting checklist

| Symptom | Check |
|---|---|
| MCP tools timeout | `system_get_metadata` — is DB connected? |
| Auth not working | `auth_get_config` — are settings correct? |
| OAuth failing | `auth_list_oauth_configs` — is provider enabled? |
| Storage uploads fail | Check `STORAGE_PROVIDER` and credentials |
| AI features error | Check `OPENROUTER_API_KEY` is set |
| Functions won't deploy | Check `DENO_RUNTIME_URL` is reachable |
| Emails not sent | Check SMTP env vars, try `system_send_email` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mauricioperera) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
