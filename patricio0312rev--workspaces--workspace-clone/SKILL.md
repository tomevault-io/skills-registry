---
name: workspace-clone
description: Clone one or all repositories configured in a workspace to their designated paths. Use when user says "clone repos", "clone all", or wants to download workspace repositories. Use when this capability is needed.
metadata:
  author: patricio0312rev
---

# Skill: Workspace Clone

## Description

Clone one or all repositories configured in a workspace to their designated paths.

## Arguments

- `[project]` - Name of a specific project to clone
- `all` - Clone all projects (default if no argument)

## Instructions

When the user wants to clone repositories:

### Step 1: Detect Workspace

Identify the current workspace. If none detected, ask the user to specify one or run `/workspaces:init`.

### Step 2: Parse Configuration

Read the workspace configuration and get the list of projects with their:
- Repository URL
- Target path

### Step 3: Clone Repositories

For each repository:

1. **Check if already cloned** - Skip if directory exists with `.git`
2. **Create parent directory** if needed
3. **Clone the repository**
4. **Report status**

### Output Format

```
📥 Cloning workspace repositories...

Cloning api...
  git clone git@github.com:acme/api.git ~/work/acme/api
  ✓ api cloned successfully

Cloning admin...
  git clone git@github.com:acme/admin.git ~/work/acme/admin
  ✓ admin cloned successfully

✓ homepage already exists at ~/work/acme/homepage

Summary:
  • Cloned: api, admin
  • Skipped: homepage (already exists)
  • Failed: 0
```

### Post-Clone Actions

After successful cloning, suggest:
```
Run '/workspaces:setup all' to install dependencies and configure projects.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patricio0312rev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
