---
name: authoring-dags
description: Workflow and best practices for writing Apache Airflow DAGs. Use when the user wants to create a new DAG, write pipeline code, or asks about DAG patterns and conventions. For testing and debugging DAGs, see the testing-dags skill. Use when this capability is needed.
metadata:
  author: neversight
---

# DAG Authoring Skill

This skill guides you through creating and validating Airflow DAGs using best practices and MCP tools.

> **For testing and debugging DAGs**, see the **testing-dags** skill which covers the full test → debug → fix → retest workflow.

---

## ⚠️ CRITICAL WARNING: Use MCP Tools, NOT CLI Commands ⚠️

> **STOP! Before running ANY Airflow-related command, read this.**
>
> You MUST use MCP tools for ALL Airflow interactions. CLI commands like `astro dev run`, `airflow dags`, or shell commands to read logs are **FORBIDDEN**.
>
> **Why?** MCP tools provide structured, reliable output. CLI commands are fragile, produce unstructured text, and often fail silently.

---

## CLI vs MCP Quick Reference

**ALWAYS use Airflow MCP tools. NEVER use CLI commands.**

| ❌ DO NOT USE | ✅ USE INSTEAD |
|---------------|----------------|
| `astro dev run dags list` | `list_dags` MCP tool |
| `airflow dags list` | `list_dags` MCP tool |
| `astro dev run dags test` | `trigger_dag_and_wait` MCP tool |
| `airflow tasks test` | `trigger_dag_and_wait` MCP tool |
| `cat` / `grep` on Airflow logs | `get_task_logs` MCP tool |
| `find` in dags folder | `list_dags` or `explore_dag` MCP tool |
| Any `astro dev run ...` | Equivalent MCP tool |
| Any `airflow ...` CLI | Equivalent MCP tool |
| `ls` on `/usr/local/airflow/dags/` | `list_dags` or `explore_dag` MCP tool |
| `cat ... \| jq` to filter MCP results | Read the JSON directly from MCP response |

**Remember:**
- ✅ Airflow is ALREADY running — the MCP server handles the connection
- ❌ Do NOT attempt to start, stop, or manage the Airflow environment
- ❌ Do NOT use shell commands to check DAG status, logs, or errors
- ❌ Do NOT use bash to parse or filter MCP tool results — read the JSON directly
- ❌ Do NOT use `ls`, `find`, or `cat` on Airflow container paths (`/usr/local/airflow/...`)
- ✅ ALWAYS use MCP tools — they return structured JSON you can read directly

## Workflow Overview

```
┌─────────────────────────────────────┐
│ 1. DISCOVER                         │
│    Understand codebase & environment│
└─────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ 2. PLAN                             │
│    Propose structure, get approval  │
└─────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ 3. IMPLEMENT                        │
│    Write DAG following patterns     │
└─────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ 4. VALIDATE                         │
│    Check import errors, warnings    │
└─────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ 5. TEST (with user consent)         │
│    Trigger, monitor, check logs     │
└─────────────────────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ 6. ITERATE                          │
│    Fix issues, re-validate          │
└─────────────────────────────────────┘
```

---

## Phase 1: Discover

Before writing code, understand the context.

### Explore the Codebase

Use file tools to find existing patterns:
- `Glob` for `**/dags/**/*.py` to find existing DAGs
- `Read` similar DAGs to understand conventions
- Check `requirements.txt` for available packages

### Query the Airflow Environment

Use MCP tools to understand what's available:

| Tool | Purpose |
|------|---------|
| `list_connections` | What external systems are configured |
| `list_variables` | What configuration values exist |
| `list_providers` | What operator packages are installed |
| `get_airflow_version` | Version constraints and features |
| `list_dags` | Existing DAGs and naming conventions |
| `list_pools` | Resource pools for concurrency |

**Example discovery questions:**
- "Is there a Snowflake connection?" → `list_connections`
- "What Airflow version?" → `get_airflow_version`
- "Are S3 operators available?" → `list_providers`

---

## Phase 2: Plan

Based on discovery, propose:

1. **DAG structure** - Tasks, dependencies, schedule
2. **Operators to use** - Based on available providers
3. **Connections needed** - Existing or to be created
4. **Variables needed** - Existing or to be created
5. **Packages needed** - Additions to requirements.txt

**Get user approval before implementing.**

---

## Phase 3: Implement

Write the DAG following best practices (see below). Key steps:

1. Create DAG file in appropriate location
2. Update `requirements.txt` if needed
3. Save the file

---

## Phase 4: Validate

**Use the Airflow MCP as a feedback loop. Do NOT use CLI commands.**

### Step 1: Check Import Errors

After saving, call the MCP tool (Airflow will have already parsed the file):

**MCP tool:** `list_import_errors`

- If your file appears → **fix and retry**
- If no errors → **continue**

Common causes: missing imports, syntax errors, missing packages.

### Step 2: Verify DAG Exists

**MCP tool:** `get_dag_details(dag_id="your_dag_id")`

Check: DAG exists, schedule correct, tags set, paused status.

### Step 3: Check Warnings

**MCP tool:** `list_dag_warnings`

Look for deprecation warnings or configuration issues.

### Step 4: Explore DAG Structure

**MCP tool:** `explore_dag(dag_id="your_dag_id")`

Returns in one call: metadata, tasks, dependencies, source code.

---

## Phase 5: Test

> **📘 See the testing-dags skill for comprehensive testing guidance.**

Once validation passes, test the DAG using the workflow in the **testing-dags** skill:

1. **Get user consent** — Always ask before triggering
2. **Trigger and wait** — Use `trigger_dag_and_wait(dag_id, timeout=300)`
3. **Analyze results** — Check success/failure status
4. **Debug if needed** — Use `diagnose_dag_run` and `get_task_logs`

### Quick Test (Minimal)

```
# Ask user first, then:
trigger_dag_and_wait(dag_id="your_dag_id", timeout=300)
```

For the full test → debug → fix → retest loop, see **testing-dags**.

---

## Phase 6: Iterate

If issues found:
1. Fix the code
2. Check for import errors with `list_import_errors` MCP tool
3. Re-validate using MCP tools (Phase 4)
4. Re-test using the **testing-dags** skill workflow (Phase 5)

**Never use CLI commands to check status or logs. Always use MCP tools.**

---

## MCP Tools Quick Reference

| Phase | Tool | Purpose |
|-------|------|---------|
| Discover | `list_connections` | Available connections |
| Discover | `list_variables` | Configuration values |
| Discover | `list_providers` | Installed operators |
| Discover | `get_airflow_version` | Version info |
| Validate | `list_import_errors` | Parse errors (check first!) |
| Validate | `get_dag_details` | Verify DAG config |
| Validate | `list_dag_warnings` | Configuration warnings |
| Validate | `explore_dag` | Full DAG inspection |

> **Testing tools** — See the **testing-dags** skill for `trigger_dag_and_wait`, `diagnose_dag_run`, `get_task_logs`, etc.

---

## Best Practices & Anti-Patterns

For detailed code examples and patterns, see **[reference/best-practices.md](reference/best-practices.md)**.

Key topics covered:
- TaskFlow API usage
- Credentials management (connections, variables)
- Provider operators
- Idempotency patterns
- Data intervals
- Task groups
- Setup/Teardown patterns
- Data quality checks
- Anti-patterns to avoid

---

## Related Skills

- **testing-dags**: For testing DAGs, debugging failures, and the test → fix → retest loop
- **debugging-dags**: For troubleshooting failed DAGs
- **migrating-airflow-2-to-3**: For migrating DAGs to Airflow 3

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
