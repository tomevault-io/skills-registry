---
name: developing-with-ai-sdk
description: Builds AI agents, generates text, chat, images, audio, embeddings, etc using Laravel AI SDK. Supports structured output, streaming, tools, RAG.
metadata:
  author: jdecode
---

# Laravel AI SDK

Unified API for AI (agents, images, audio, embeddings, etc).

## Documentation
**CRITICAL**: Package is new. ALWAYS search docs (`search-docs`) before implementing.
- Use broad queries (e.g. `['agents', 'streaming']`).
- Do NOT include package names in queries.

## Workflow
- **Text/Chat**: Agent + `Promptable`.
- **Chat History**: Agent + `Conversational` or `RemembersConversations`.
- **Structured Output**: Agent + `HasStructuredOutput`.
- **Images**: `Image::of()->generate()`.
- **Audio**: `Audio::of()->generate()`.
- **Transcription**: `Transcription::fromPath()->generate()`.
- **Embeddings**: `Embeddings::for()->generate()`.
- **Reranking**: `Reranking::of()->rerank()`.
- **Files**: `Document::fromPath()->put()`.
- **vectors**: `Stores::create()`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jdecode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
