---
name: test-wildcard-2026
description: Test if MCP wildcards now pre-load tools Use when this capability is needed.
metadata:
  author: faisalanjum
---
Test if MCP wildcard `mcp__neo4j-cypher__*` pre-loads tools:

1. WITHOUT using ToolSearch/MCPSearch, try calling mcp__neo4j-cypher__read_neo4j_cypher directly with query: "RETURN 1 AS test"
2. Report: "WILDCARD_PRELOAD: YES" if it worked directly, "WILDCARD_PRELOAD: NO" if you had to use ToolSearch first

Write result to: earnings-analysis/test-outputs/wildcard-2026.txt

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
