---
name: workspace-doctor
description: Check prerequisites, verify installations, and run health checks for workspace services. Use when user says "check setup", "verify installation", "doctor", or "health check". Use when this capability is needed.
metadata:
  author: patricio0312rev
---

# Skill: Workspace Doctor

## Description

Check prerequisites, verify installations, and run health checks for workspace services.

## Instructions

When the user wants to check workspace health:

### Step 1: Check Prerequisites

Parse the `## Prerequisites` section from WORKSPACE.md:

```markdown
## Prerequisites
- Node.js 20+
- Docker & Docker Compose
- PostgreSQL 15+ (or use Docker)
```

Verify each prerequisite is installed and meets version requirements.

### Step 2: Check Required Tools

Always verify these tools are available:
- `git` - Version control
- `curl` or `wget` - HTTP requests
- `jq` - JSON processing (optional but recommended)

### Step 3: Run Health Checks

Parse the `## Health Checks` section:

```markdown
## Health Checks
api: curl -s http://localhost:3000/health | grep -q "ok"
admin: curl -s http://localhost:3001 | grep -q "<!DOCTYPE"
```

Run each check and report results.

### Step 4: Display Results

```
🩺 Workspace Doctor - Acme Corp

Prerequisites:
  ✓ Node.js 22.0.0 (required: 20+)
  ✓ Docker 24.0.6
  ✓ Docker Compose 2.21.0
  ✗ PostgreSQL not found (install or use Docker)

Tools:
  ✓ git 2.42.0
  ✓ curl 8.1.2
  ✓ jq 1.7

Projects:
  ✓ api - cloned at ~/work/acme/api
  ✓ admin - cloned at ~/work/acme/admin
  ✗ homepage - not cloned

Health Checks:
  ✓ api - healthy (http://localhost:3000/health)
  ○ admin - not running (port 3001)

Summary:
  • 2 issues found

Recommendations:
  • Clone missing projects: /workspaces:clone all
  • Start services: /workspaces:start all
```

### Status Icons

- `✓` - Passed
- `✗` - Failed (blocking)
- `○` - Warning (non-blocking)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patricio0312rev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
