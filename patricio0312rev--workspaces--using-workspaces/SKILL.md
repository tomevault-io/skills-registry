---
name: using-workspaces
description: Overview and getting started guide for the workspaces plugin. Use when user asks "how do I use workspaces", "workspaces help", or "getting started with workspaces". Use when this capability is needed.
metadata:
  author: patricio0312rev
---

# Skill: Using Workspaces

## Description

This skill provides an overview of the workspaces plugin and how to use it effectively for managing multi-project/multi-repo workflows.

## Instructions

When the user asks about using the workspaces plugin, provide this overview:

### Workspaces Overview

The **workspaces** plugin helps you manage multiple related repositories that belong to the same company or project. Perfect for teams working with microservices, polyrepos, or any multi-project setup.

### Quick Start

1. **Initialize a workspace**: `/workspaces:init`
   - Creates a new workspace configuration
   - Add your projects with their repos, paths, and ports

2. **Clone repositories**: `/workspaces:clone all`
   - Clone all configured repos to their designated paths

3. **Set up projects**: `/workspaces:setup all`
   - Run setup scripts (install deps, migrations, etc.)

4. **Verify everything**: `/workspaces:doctor`
   - Check prerequisites and run health checks

5. **Check status**: `/workspaces:status`
   - See git status, branches, and running services across all projects

### Configuration

Workspace configs are stored in `~/.claude/workspaces/<workspace-name>/`:
- `WORKSPACE.md` - Main configuration with projects, relationships, and commands

### Available Commands

| Command | Description |
|---------|-------------|
| `/workspaces:init` | Create a new workspace |
| `/workspaces:status` | Show status across all projects |
| `/workspaces:clone` | Clone repositories |
| `/workspaces:setup` | Run setup scripts |
| `/workspaces:start` | Start services |
| `/workspaces:stop` | Stop services |
| `/workspaces:affected` | Show dependency graph |
| `/workspaces:search` | Search across all projects |
| `/workspaces:doctor` | Check prerequisites and health |

### Tips

- Use `/workspaces:affected api` to see what depends on the API before making breaking changes
- Use `/workspaces:search "TODO"` to find TODOs across all projects
- Run `/workspaces:doctor` when something isn't working

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patricio0312rev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
