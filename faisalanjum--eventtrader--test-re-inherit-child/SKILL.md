---
name: test-re-inherit-child
description: Retest 2026-02-05: Child WITH MCP - tests own access Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: Child with MCP tool

WITHOUT using ToolSearch, try mcp__neo4j-cypher__read_neo4j_cypher:
- Query: RETURN 'child_mcp' AS source
- Reply with: "CHILD_MCP: WORKS" or "CHILD_MCP: NEEDS_TOOLSEARCH"
- Include query result

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
