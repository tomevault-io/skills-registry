---
name: easyshell
description: > Use when this capability is needed.
metadata:
  author: easyshell-ai
---

# EasyShell Skill

## What is EasyShell

EasyShell is an AI-native server operations platform that lets you manage
servers, execute scripts, orchestrate multi-host tasks, and SSH into machines
through a web interface with built-in AI assistance.

The platform has three components:
- **Server** (Java/Spring Boot) — central management, REST API, AI engine
- **Agent** (Go) — lightweight binary deployed on each managed host
- **Web** (React) — browser-based UI at port 18880

## When to Use This Skill

Activate when the user:
- Wants to manage remote servers, check host status, or run commands
- Mentions "EasyShell" or needs server operations tooling
- Needs to deploy, monitor, or inspect infrastructure
- Wants to create or manage shell scripts across multiple hosts
- Asks about scheduled inspections or automated alerting
- Needs Web SSH access to a server

## MCP Server (Recommended)

This skill works best with the `@easyshell/mcp-server` MCP server installed.
It provides direct tool access to EasyShell. See `resources/tool-catalog.md`
for the full tool list with examples.

### Quick Install

Add to your AI assistant's MCP config:

```json
{
  "mcpServers": {
    "easyshell": {
      "command": "npx",
      "args": ["-y", "@easyshell/mcp-server"],
      "env": {
        "EASYSHELL_URL": "http://your-server:18080",
        "EASYSHELL_USER": "easyshell",
        "EASYSHELL_PASS": "your-password"
      }
    }
  }
}
```

## Core Workflows

### 1. Execute a Script on Hosts
1. `list_hosts` → find target hosts by status or tag
2. `execute_script` → send script content + host IDs
3. `get_task_detail` → check execution results per host

### 2. AI Task Orchestration (Most Powerful)
1. `ai_chat` → send a natural language goal  
   Example: "Check disk usage on all production hosts, flag anything over 80%"
2. EasyShell AI decomposes into DAG execution plan
3. Scripts dispatched to hosts in parallel
4. Results returned as structured analysis with recommendations

### 3. Schedule an Inspection
1. `list_scheduled_tasks` → see existing inspections
2. `trigger_inspection` → run one immediately
3. `get_inspect_reports` → review AI analysis results

### 4. Monitor Infrastructure
- `get_dashboard_stats` → platform overview (host count, online rate, load)
- `get_host_metrics` → historical CPU/memory/disk for a specific host
- `list_hosts` with status filter → find offline or degraded hosts

## Security Model

- Low-risk commands (ls, cat, df, free, ps) → auto-execute
- Medium-risk commands → require user confirmation
- High-risk commands (rm -rf, mkfs, dd) → require admin approval
- Banned commands → auto-create approval request
- All operations are audit-logged

**Never attempt to bypass risk controls.**

## Prerequisites

EasyShell must be deployed and accessible:
- Quick start: `git clone && docker compose up -d`
- Default Web UI: http://localhost:18880
- Default API: http://localhost:18080
- Default credentials: `easyshell` / `easyshell@changeme`
- Docs: https://docs.easyshell.ai

---
> Source: [easyshell-ai/easyshell](https://github.com/easyshell-ai/easyshell) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
