---
name: test-3layer-bottom
description: Layer 3 (bottom) of 3-layer test - executes MCP query Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Layer 3 Bottom

**Goal**: Execute MCP query and return result to Layer 2.

## Task

1. Use MCPSearch to load `mcp__neo4j-cypher__read_neo4j_cypher`
2. Execute: `MATCH (c:Company) RETURN c.ticker LIMIT 3`
3. Write to: `earnings-analysis/test-outputs/3layer-bottom.txt`

Return format: "LAYER3_RESULT: [list of tickers]"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
