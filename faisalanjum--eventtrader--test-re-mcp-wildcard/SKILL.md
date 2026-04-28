---
name: test-re-mcp-wildcard
description: Retest 2026-02-05: Do MCP wildcards pre-load tools? Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: MCP wildcard pre-load

Your allowed-tools has mcp__neo4j-cypher__* (wildcard). Test:

WITHOUT using ToolSearch, try calling mcp__neo4j-cypher__read_neo4j_cypher directly:
- Query: RETURN 1 AS test
- If it works without ToolSearch: write "WILDCARD_PRELOAD: YES" to earnings-analysis/test-outputs/test-re-mcp-wildcard.txt
- If you need ToolSearch: write "WILDCARD_PRELOAD: NO"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
