---
name: terraform
description: >- Use when this capability is needed.
metadata:
  author: bendrucker
---

# Terraform

## Tool Selection

- **MCP tools** (`mcp__terraform`): workspace listing, run management, provider/module registry lookups
- **curl**: plan and apply log retrieval — the MCP server does not expose these endpoints

## Run Monitoring

Use MCP tools to find and inspect runs:

1. `list_workspaces` to find the workspace by name
2. `list_runs` with the workspace ID to see recent runs
3. `get_run_details` with the run ID for status, relationships, and timestamps

The run details include `relationships.plan.data.id` and `relationships.apply.data.id` — use these to fetch logs.

## Fetching Plan/Apply Logs

The MCP server does not expose log retrieval. Use the TFC API directly:

```bash
# Get the apply details (includes log-read-url)
curl -s \
  -H "Authorization: Bearer $TFE_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  https://app.terraform.io/api/v2/applies/{apply-id} | jq -r '.data.attributes."log-read-url"'
```

Then fetch the raw logs from the archivist URL (no auth needed — pre-signed):

```bash
curl -s "$(archivist_url)"
```

The same pattern works for plans via `/api/v2/plans/{plan-id}`.

See [references/api.md](references/api.md) for full API details.

## Authentication

`$TFE_TOKEN` is sourced from:
- `terraform login` (stored at `~/.terraform.d/credentials.tfrc.json`)
- Or set directly as an environment variable

The MCP server reads `TFE_TOKEN` from the environment. For curl calls, use it as a Bearer token.

## Registry

Provider and module searches are available via MCP tools (`search_providers`, `search_modules`, `provider_details`, `module_details`). No curl needed for registry operations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
