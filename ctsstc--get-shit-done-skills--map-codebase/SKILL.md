---
name: gsdmap-codebase
description: Analyze codebase with parallel mapper agents Use when this capability is needed.
metadata:
  author: ctsstc
---

# GSD Map Codebase

Analyzes existing codebase using parallel mapper agents.

## When to Use

- Understanding an existing codebase structure
- Starting work on a brownfield project
- Documenting technology stack and architecture

## Process

1. Check for existing .planning/codebase/ directory
2. Create directory structure if needed
3. Spawn 4 parallel mapper agents (tech, arch, quality, concerns)
4. Wait for all agents to complete
5. Verify all 7 documents exist
6. Commit codebase map to git

## Output Documents

- STACK.md, INTEGRATIONS.md (tech focus)
- ARCHITECTURE.md, STRUCTURE.md (arch focus)
- CONVENTIONS.md, TESTING.md (quality focus)
- CONCERNS.md (concerns focus)

## Success Criteria

All 7 documents written with content and committed to git.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ctsstc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
