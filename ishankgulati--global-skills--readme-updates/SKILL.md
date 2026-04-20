---
name: readme-updates
description: Guidelines for ensuring documentation stays synchronous with code changes Use when this capability is needed.
metadata:
  author: ishankgulati
---

# README Update Guidelines

## Update Policy
- **Regular Updates**: The `README.md` must be updated immediately following any significant changes to the codebase.
- **Definition of "Big Changes"**:
  - Changes to build/run instructions.
  - New or modified infrastructure dependencies.
  - Changes to environment variable requirements.
  - Major refactoring of core logic.

## maintenance Checklist
When submitting Changes:
1. [ ] Check if `Getting Started` instructions are broken.
2. [ ] Verify `Configuration` section covers new flags/env vars.
3. [ ] Update `Architecture` section if components changed.

## Goal
Documentation should not lag behind code. The README is the primary entry point.

## README Template
When creating a new repository, use this structure:

```markdown
# Project Name

> Short description of what the project does.

## Description
Why is this project useful? What problem does it solve?

## Getting Started
How to install and run the project.

### Prerequisites
- Dependency 1
- Dependency 2

### Installation
Command to install dependencies.

## Usage
How to use the project.

## Support
Where to get help or report bugs.

## Maintainers
Who maintains this project.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ishankgulati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
