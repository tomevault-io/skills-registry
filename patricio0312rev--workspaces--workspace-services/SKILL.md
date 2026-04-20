---
name: workspace-services
description: Start and stop development services for workspace projects. Use when user says "start services", "stop services", "start all", or "stop all". Use when this capability is needed.
metadata:
  author: patricio0312rev
---

# Skill: Workspace Services

## Description

Start and stop services for workspace projects. Manages development servers and related services.

## Arguments

- `start [project|all]` - Start services
- `stop [project|all]` - Stop services

## Instructions

### Starting Services

When the user wants to start services:

#### Step 1: Check for Shared Command

Look for `start-all` in the `## Commands` section of WORKSPACE.md:

```markdown
## Commands
start-all: cd ~/work/acme && docker-compose up -d
```

If a shared command exists, offer to use it.

#### Step 2: Start Individual Projects

If no shared command or user prefers individual starts:

1. Find the project's start command (check package.json for `dev` or `start` scripts)
2. Start the service
3. Verify the port becomes active

#### Output Format for Start

```
🚀 Starting workspace services...

Starting api...
  cd ~/work/acme/api && npm run dev
  ✓ api started on port 3000

Starting admin...
  cd ~/work/acme/admin && npm run dev
  ✓ admin started on port 3001

Port status:
  • api: :3000 ✓ running
  • admin: :3001 ✓ running
```

### Stopping Services

#### Step 1: Check for Shared Command

Look for `stop-all` in `## Commands`.

#### Step 2: Stop Individual Projects

If no shared command, stop services by terminating processes on configured ports.

#### Output Format for Stop

```
🛑 Stopping workspace services...

Stopping api (port 3000)...
  ✓ Stopped

Stopping admin (port 3001)...
  ✓ Stopped
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patricio0312rev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
