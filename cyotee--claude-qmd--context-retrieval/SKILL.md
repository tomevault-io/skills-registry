---
name: context-retrieval
description: Automatically retrieves relevant context from QMD before complex tasks. Enhances responses with indexed documentation. Use when this capability is needed.
metadata:
  author: cyotee
---

# Context Retrieval Skill

Augment your knowledge with indexed documentation before responding.

## When to Activate

Retrieve context when:
- User asks about a specific DeFi protocol
- User asks "how do I..." for documented features
- Task involves multiple protocols
- User references plugin functionality

## How to Retrieve

### 1. Detect Query Type

| Query Contains | Collection to Search |
|----------------|---------------------|
| Protocol name (Aave, Uniswap, Balancer...) | `protocols` |
| Plugin name or slash command | `plugins` |
| Project-specific terms | `project` |
| General "how do I" | `protocols` then `plugins` |

### 2. Execute Search

Use the MCP tool if available:

```
qmd_query(collection: "<collection>", query: "<derived query>", limit: 5)
```

Or fall back to CLI:

```bash
qmd query "<query>" --collection <collection> --limit 5 --format json
```

### 3. Integrate Context

When context is retrieved:
- Summarize relevant findings internally
- Use retrieved information to enhance response
- Cite source documents when directly using information
- Don't overwhelm response with raw search results

## Example Flows

**User asks: "How do I implement a flash loan?"**

1. Detect: Protocol question about flash loans
2. Search: `qmd_query(collection: "protocols", query: "flash loan implementation", limit: 5)`
3. Results: Aave flash loan docs, Balancer flash loan docs
4. Respond: Use retrieved context to give accurate implementation guidance

**User asks: "What commands does the backlog plugin have?"**

1. Detect: Plugin question
2. Search: `qmd_query(collection: "plugins", query: "backlog commands", limit: 5)`
3. Results: backlog plugin command docs
4. Respond: List commands with accurate descriptions from docs

## Don't Retrieve When

- Simple conversational responses
- User explicitly states they don't need documentation
- Task is clearly not documentation-related
- Same query was just retrieved (avoid redundancy)

## Transparency

If retrieval significantly influenced your response, mention it:

> "Based on the indexed Aave V3 documentation..."

But don't over-explain the retrieval process itself.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cyotee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
