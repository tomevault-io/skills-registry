---
name: test-inherit-parent2
description: Parent skill WITH MCP tool - calls child that DOES NOT have MCP tool Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test Inherit Parent 2

**Goal**: Test if child inherits MCP tool from parent.

## Task

1. Parent HAS mcp__neo4j-cypher__read_neo4j_cypher in allowed-tools
2. Call `/test-inherit-child2` (which does NOT have MCP in allowed-tools)
3. Report whether child could use MCP tool

Write to: `earnings-analysis/test-outputs/inherit-parent2-result.txt`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
