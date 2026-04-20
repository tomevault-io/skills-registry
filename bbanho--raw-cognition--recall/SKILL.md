---
name: recall
description: Search the agent's long-term memory (Cortical Stack/Qdrant) for refined concepts, past thoughts, and synthesized knowledge. Use this skill when the user asks about prior work, decisions, or specialized knowledge stored in the vector database that isn't immediately available in Markdown files. Use when this capability is needed.
metadata:
  author: bbanho
---

# Recall Skill (Cortical Stack)

Search the long-term memory stored in the local Qdrant instance. This skill uses the `nomic-embed-text` model via Ollama to perform semantic searches against the `openclaw_memory` collection.

## Quick Start

Run the recall script with your search query:

```bash
./skills/recall/scripts/recall.sh "your search query"
```

## When to Use

- When asked "What do you remember about X?"
- To verify architectural decisions made in previous sessions.
- To retrieve snippets of articles or thoughts that were indexed by the Cortex.
- When `memory_search` (Markdown-only) doesn't return enough depth.

## Implementation Details

The skill executes a two-step process:
1.  **Embedding:** Generates a vector for the query using `nomic-embed-text` via the local Ollama API.
2.  **Vector Search:** Queries the local Qdrant database for the top 5 most semantically similar points with a score threshold.

## Troubleshooting

- **Ollama Error:** Ensure the Ollama service is running on port 11434 and the `nomic-embed-text` model is installed.
- **Qdrant Error:** Ensure the Qdrant container/service is running on port 6333.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bbanho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
