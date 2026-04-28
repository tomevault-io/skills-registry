---
name: test-mcp-in-fork
description: Test if MCP tools can be accessed from forked skill context Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test MCP Access in Fork

**Goal**: Verify if MCP tools can be loaded and executed from a forked skill.

## Task

1. Use MCPSearch to load `mcp__neo4j-cypher__read_neo4j_cypher`
2. If loaded, execute a simple query: `MATCH (c:Company {ticker: 'AAPL'}) RETURN c.name LIMIT 1`
3. Write results to: `earnings-analysis/test-outputs/mcp-in-fork-result.txt`

Include in output:
- Whether MCPSearch was available
- Whether MCP tool was loaded successfully
- Whether query executed successfully
- The query result (if any)
- List of tools you have access to

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
