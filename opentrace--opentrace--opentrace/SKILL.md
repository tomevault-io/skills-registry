---
name: explore-code
description: | Use when this capability is needed.
metadata:
  author: opentrace
---

The user wants to explore or understand something about the codebase. Use the OpenTrace knowledge graph to answer their question.

## Query
$ARGUMENTS

## Instructions

Use the OpenTrace MCP tools to investigate. Follow this approach:

1. **Orient** — Call `get_stats` to see what's indexed (node types and counts). This tells you what's available in the graph.

2. **Search** — Use `search_graph` to find nodes matching the user's query. Use `nodeTypes` to filter when appropriate:
   - Architecture questions → "Service,Repository"
   - Code questions → "Class,Function,Module"
   - File/directory browsing → "File,Directory"
   - Data questions → "Database,DBTable"
   - Infrastructure → "Deployment,Cluster,Namespace"
   - Unsure → omit `nodeTypes` to search everything

3. **Inspect** — Use `get_node` on the best matches to see full details and neighbors.

4. **Trace** — Use `traverse_graph` to follow relationships:
   - "What calls X?" → `direction: incoming`
   - "What does X depend on?" → `direction: outgoing`
   - "What's in X?" → `direction: outgoing` (CONTAINS relationships)
   - "How are X and Y connected?" → traverse from both and find common nodes

5. **List** — Use `list_nodes` when the user wants to see all items of a type (e.g. all services, all files in a directory).

6. **Read source** — If nodes have a `path` property and the user needs code details, use `Read` to show the source.

## Response Guidelines

- Lead with a clear, concise answer before showing supporting details
- Show relationships as paths: `ServiceA --CALLS--> ServiceB --READS--> DatabaseC`
- Group related information (structure, dependencies, consumers)
- Offer to drill deeper if the graph reveals more to explore
- If the graph doesn't have the data, say so and fall back to `Glob`/`Grep`/`Read`

---
> Source: [opentrace/opentrace](https://github.com/opentrace/opentrace) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
