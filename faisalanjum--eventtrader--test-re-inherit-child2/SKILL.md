---
name: test-re-inherit-child2
description: Retest 2026-02-05: Child WITHOUT MCP - tests if parent's MCP inherited Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: Child without MCP (parent has it)

WITHOUT using ToolSearch, try mcp__neo4j-cypher__read_neo4j_cypher:
- Query: RETURN 'inherited' AS source
- Reply with: "CHILD2_MCP: INHERITED" if it worked without ToolSearch
- Reply with: "CHILD2_MCP: NOT_INHERITED" if tool not available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
