---
name: learning
description: Updates local AgentDB vector store with new case patterns while monitoring for bias introduction Use when this capability is needed.
metadata:
  author: do-ops885
---

## What I do

I update the vector store with the new case pattern for future RAG retrieval. I monitor the update for potential bias introduction and ensure diverse demographic representation in stored patterns.

## When to use me

Use this when:

- Recommendations are generated and you need to update the knowledge base
- You're storing this case for future similar case retrieval
- You need to ensure the vector store remains bias-free

## Key Concepts

- **Vector Store Update**: Add new embedding to AgentDB
- **Bias Monitoring**: Check for demographic skew in stored patterns
- **Case Pattern**: Embedding + metadata for future retrieval
- **learning_updated**: State flag after update complete

## Source Files

- `services/agentDB.ts`: Vector store operations
- `types.ts`: ReasoningPattern interface

## Code Patterns

- Generate embedding for the current case
- Store with demographic metadata and outcomes
- Validate demographic diversity in storage

## Operational Constraints

- Must maintain demographic diversity in vector store
- Monitor for bias drift over time
- Log all updates for audit trail

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/do-ops885) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
