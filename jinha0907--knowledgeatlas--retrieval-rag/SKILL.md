---
name: retrieval-rag
description: Use this when implementing search/RAG: embedding query, pgvector similarity search, optional keyword search, and returning answers with citations (document/block references).
metadata:
  author: jinha0907
---

## Retrieval policy (MVP)
- topK vector search on embedding table joined with chunk/content_block.
- Return: chunks + doc title + block reference so UI can jump to evidence.
- Keep response deterministic: include score, document_id, block_id.

## Guardrails
- Never hallucinate citations. If no evidence, say so and return empty citations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jinha0907) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
