---
name: setup-aspire
description: Install the .NET Aspire CLI tool for MCP integration Use when this capability is needed.
metadata:
  author: melodic-software
---

# /dotnet:setup-aspire

Install the .NET Aspire CLI, required for the aspire MCP server.

## Workflow

### Step 1: Check current state

```bash
dotnet tool list -g | grep -i aspire || echo "Aspire CLI not installed"
```

### Step 2: Install

```bash
dotnet tool install --global aspire.cli
```

If already installed and needs updating:

```bash
dotnet tool update --global aspire.cli
```

### Step 3: Verify

```bash
aspire --version
```

### Step 4: Report

Tell the user to restart Claude Code so the aspire MCP server can reconnect with the now-available CLI tool.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
