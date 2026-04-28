---
name: embedding-strategy
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Embedding Strategy

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- **Asymmetric Embedding Strategy**:
  - RETRIEVAL_DOCUMENT task_type for document ingestion
  - RETRIEVAL_QUERY task_type for query embeddings
  - Semantic mismatch prevention
- Concurrent online embeddings (never use batch API for documents)
- Embedding model selection and configuration
- Asymmetric task_type validation and enforcement

## Critical Pattern

Documents and queries must use different task_types for optimal semantic search. Batch API defaults to RETRIEVAL_QUERY which causes semantic mismatch and poor retrieval quality.

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
