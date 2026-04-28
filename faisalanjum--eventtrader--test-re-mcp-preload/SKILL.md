---
name: test-re-mcp-preload
description: Retest 2026-02-05: Does allowed-tools pre-load MCP tools? Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: MCP pre-load via allowed-tools

WITHOUT using ToolSearch or MCPSearch, try calling mcp__neo4j-cypher__read_neo4j_cypher directly:
- Query: RETURN 1 AS test
- If it works without ToolSearch: write "MCP_PRELOAD: WORKS" to earnings-analysis/test-outputs/test-re-mcp-preload.txt
- If you need ToolSearch first: write "MCP_PRELOAD: NEEDS_TOOLSEARCH"
- Include the query result in the file

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
