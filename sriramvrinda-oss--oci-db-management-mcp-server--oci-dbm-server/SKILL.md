---
name: oci-dbm-server
description: > Use when this capability is needed.
metadata:
  author: sriramvrinda-oss
---

# OCI DB Management — Start the MCP Server

## What it does

Starts the OCI Database Management MCP server as a local process. Once running, Claude has
access to all 24 DBM tools in this session:
- Fleet inventory and health
- AWR performance reports and wait event analysis
- DB load (CPU vs I/O), physical reads, commit rate
- Alert log triage
- Parameter change detection
- Cursor cache inspection
- SQL Tuning Advisor (start task, get findings, read recommendations)
- DB Management Jobs (create, monitor, history)
- Identity / compartment access

## Prerequisites

- `oci-setup` must have completed successfully (valid OCI credentials)
- Python 3.9+ and `uv` installed, or the package installed directly via pip

---

## Starting the server

### Option A — via uvx (recommended, no install needed)

```bash
uvx oci-db-management-mcp-server
```

### Option B — installed package

```bash
pip install oci-db-management-mcp-server
oci-db-management-mcp-server
```

### Option C — from source (development)

```bash
cd /path/to/oci-db-management-mcp-server
uv run python -m oci_db_management
```

The server starts on **STDIO transport** by default (required for Claude Code MCP integration).

---

## Wiring into Claude

After the server process is running, register it in Claude's MCP config:

```json
{
  "mcpServers": {
    "oci-db-management": {
      "command": "uvx",
      "args": ["oci-db-management-mcp"],
      "env": {}
    }
  }
}
```

Config file location:
- **Mac/Linux**: `~/.claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

After adding, restart Claude Desktop or reload the MCP connection.

---

## Confirming the server is running

Once connected, the following tools should be available:

```
list_managed_databases          get_fleet_summary
get_db_load                     get_top_wait_events
get_awr_report                  list_alert_logs
get_parameter_changes           get_cursor_cache
start_sql_tuning_task           list_sql_tuning_advisor_tasks
list_sql_tuning_findings        list_sql_tuning_recommendations
get_sql_execution_plan          create_sql_job
get_sql_job                     list_sql_jobs
list_job_executions             get_job_execution
list_job_execution_summaries    list_db_management_private_endpoints
get_managed_database            list_compartments
enable_database_management      get_work_request
```

24 tools total.

Confirm by calling a low-risk tool:
```
list_managed_databases(compartment_id=<your_compartment_ocid>)
```

---

## What comes next

With the server running, offer the user the appropriate next step:

- **No ADB yet / want to onboard** → `oci-dbm-commission`
- **Want to diagnose an existing DB** → `oci-dbm-diagnose`
- **Want fleet health overview** → `oci-dbm-performance`
- **Want to run SQL** → `oci-dbm-jobs`
- **Want SQL tuning** → `oci-dbm-sql-tuning`

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `uvx: command not found` | uv not installed | `pip install uv` or install from https://astral.sh/uv |
| `ModuleNotFoundError: oci` | SDK missing | `pip install oci` |
| Tools show up but calls fail with 401 | Credentials invalid | Re-run `oci-setup` |
| Server starts then immediately exits | Port conflict or config error | Check logs, try `--debug` flag |
| `Connection refused` | Server not running | Restart the server |

---
> Source: [sriramvrinda-oss/oci-db-management-mcp-server](https://github.com/sriramvrinda-oss/oci-db-management-mcp-server) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
