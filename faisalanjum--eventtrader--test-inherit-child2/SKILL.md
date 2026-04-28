---
name: test-inherit-child2
description: Child skill WITHOUT MCP tool - tests if it inherits from parent Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test Inherit Child 2

**Goal**: Test if MCP tool is PRE-LOADED (directly usable) when only parent has it.

## Task

1. Child does NOT have mcp__neo4j-cypher__read_neo4j_cypher in allowed-tools
2. **DO NOT use MCPSearch** - try to call MCP tool directly
3. Report if it works WITHOUT MCPSearch

Write to: `earnings-analysis/test-outputs/inherit-child2-result.txt`

Return: "MCP_PRELOADED_FROM_PARENT: [yes/no] | DIRECT_ACCESS: [worked/failed]"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
