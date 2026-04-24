---
name: project-patterns
description: >- Use when this capability is needed.
metadata:
  author: francisvarga
---

# Project Patterns

## Architecture Pattern: Three-Store Model

Every ingested document automatically populates three stores:
1. **Document Store** — Raw events in time-partitioned MessagePack segments
2. **Vector Index** — Per-segment HNSW embeddings for semantic search
3. **Graph Store** — In-memory property graph for entity relationships

## Data Flow Pattern

```
Source → Ingest → Connect (extract + embed + link) → [Doc | Vec | Graph] → Compute → Catalog → Query
```

The "Connect" phase is synchronous hot-path: every batch goes through entity extraction, embedding generation, and graph edge creation before acknowledgment.

## Segment Lifecycle

```
Active (writing) → Sealed (read-only, mmap) → Archived (optional) → Evicted (deleted)
```

- Segments rotate on time boundaries (configurable, default 1 hour)
- TTL-based eviction: segments older than retention window are dropped entirely
- Eviction is O(1): drop segment file + remove per-segment vector index + prune graph edges

## Workspace Crates (18 crates)

```
crates/
├── core        # Shared types, zero internal dependencies
├── rules       # Rule loading, validation, YAML deserialization
├── segment     # Time-partitioned MessagePack storage
├── storage     # Storage abstraction layer
├── ingest      # Ingestion pipeline
├── connector   # Entity extraction, embedding, graph linking
├── graph       # In-memory property graph
├── compute     # Feature computation, anomaly detection, trends
├── catalog     # Materialized view catalog
├── llm         # Multi-provider LLM abstraction
├── agent       # Bundeswehr agent system (YAML configs, telemetry)
├── athena      # Athena connection management
├── eisenbahn   # Event bus / pub-sub infrastructure
├── queue       # Queue backends (Redis, NATS, SQS)
├── notify      # Notification system
├── mcp         # MCP tool runtime integration
├── tool-runtime# Tool execution runtime
├── cli         # CLI binary
└── server      # HTTP API server (depends on everything)
```

## Crate Dependency Direction

Dependencies flow upward. `core` has zero internal dependencies. `server` depends on everything.

```
core ← rules ← {compute, connector, ingest} ← server
core ← segment ← storage
core ← graph
core ← llm ← agent
```

## Pluggable Backend Pattern

All external services use trait-based abstraction:
- `Embedder` trait → OnnxEmbedder, OllamaEmbedder, OpenAiEmbedder
- `LlmBackend` trait → OpenAiBackend, AnthropicBackend, GeminiBackend, OllamaBackend
- Selection via config/env: `EMBEDDING_PROVIDER`, `LLM_PROVIDER`

## Entity Model

Entities extracted from documents: Member, Device, Game, Popup, Error, VipGroup, Affiliate, Currency, Platform, Provider

Join keys: memberCode, fingerprint, gameUid, rGroup, affiliateId, currency, @timestamp

## Dashboard Layer

- Chat-first interface (not traditional BI panels)
- Next.js + D3.js for visualizations
- Form → Next.js proxy → Rust backend (encrypted JSON) → Dashboard (4-layer data flow)
- No authentication — internal/trusted network deployment

## Anti-Patterns to Avoid

- Never hardcode a single LLM/embedding provider
- Never design append-only storage (must support rolling eviction)
- Never create monolithic single-crate architecture
- Never use Chart.js (always D3.js)
- Never add authentication to the dashboard
- Never modify sample data at D:\w88_data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/francisvarga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
