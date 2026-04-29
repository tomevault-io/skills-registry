---
name: rag-architecture
description: Retrieval-Augmented Generation (RAG) system design patterns, chunking strategies, embedding models, retrieval techniques, and context assembly. Use when designing RAG pipelines, improving retrieval quality, or building knowledge-grounded LLM applications. Use when this capability is needed.
metadata:
  author: melodic-software
---

# RAG Architecture

## When to Use This Skill

Use this skill when:

- Designing RAG pipelines for LLM applications
- Choosing chunking and embedding strategies
- Optimizing retrieval quality and relevance
- Building knowledge-grounded AI systems
- Implementing hybrid search (dense + sparse)
- Designing multi-stage retrieval pipelines

**Keywords:** RAG, retrieval-augmented generation, embeddings, chunking, vector search, semantic search, context window, grounding, knowledge base, hybrid search, reranking, BM25, dense retrieval

## RAG Architecture Overview

```text
┌─────────────────────────────────────────────────────────────────────┐
│                       RAG Pipeline                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────────────┐  │
│  │   Ingestion  │    │   Indexing   │    │    Vector Store      │  │
│  │   Pipeline   │───▶│   Pipeline   │───▶│    (Embeddings)      │  │
│  └──────────────┘    └──────────────┘    └──────────────────────┘  │
│         │                   │                       │               │
│    Documents           Chunks +                 Indexed             │
│                       Embeddings               Vectors              │
│                                                     │               │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────────────┐  │
│  │    Query     │    │  Retrieval   │    │   Context Assembly   │  │
│  │  Processing  │───▶│   Engine     │───▶│   + Generation       │  │
│  └──────────────┘    └──────────────┘    └──────────────────────┘  │
│         │                   │                       │               │
│    User Query          Top-K Chunks            LLM Response         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

## Document Ingestion Pipeline

### Document Processing Steps

```text
Raw Documents
      │
      ▼
┌─────────────┐
│   Extract   │ ← PDF, HTML, DOCX, Markdown
│   Content   │
└─────────────┘
      │
      ▼
┌─────────────┐
│   Clean &   │ ← Remove boilerplate, normalize
│  Normalize  │
└─────────────┘
      │
      ▼
┌─────────────┐
│   Chunk     │ ← Split into retrievable units
│  Documents  │
└─────────────┘
      │
      ▼
┌─────────────┐
│  Generate   │ ← Create vector representations
│ Embeddings  │
└─────────────┘
      │
      ▼
┌─────────────┐
│   Store     │ ← Persist vectors + metadata
│  in Index   │
└─────────────┘
```

## Chunking Strategies

### Strategy Comparison

| Strategy | Description | Best For | Chunk Size |
| -------- | ----------- | -------- | ---------- |
| **Fixed-size** | Split by token/character count | Simple documents | 256-512 tokens |
| **Sentence-based** | Split at sentence boundaries | Narrative text | Variable |
| **Paragraph-based** | Split at paragraph boundaries | Structured docs | Variable |
| **Semantic** | Split by topic/meaning | Long documents | Variable |
| **Recursive** | Hierarchical splitting | Mixed content | Configurable |
| **Document-specific** | Custom per doc type | Specialized (code, tables) | Variable |

### Chunking Decision Tree

```text
What type of content?
├── Code
│   └── AST-based or function-level chunking
├── Tables/Structured
│   └── Keep tables intact, chunk surrounding text
├── Long narrative
│   └── Semantic or recursive chunking
├── Short documents (<1 page)
│   └── Whole document as chunk
└── Mixed content
    └── Recursive with type-specific handlers
```

### Chunk Overlap

```text
Without Overlap:
[Chunk 1: "The quick brown"] [Chunk 2: "fox jumps over"]
                             ↑
               Information lost at boundary

With Overlap (20%):
[Chunk 1: "The quick brown fox"]
                    [Chunk 2: "brown fox jumps over"]
                         ↑
              Context preserved across boundaries
```

**Recommended overlap:** 10-20% of chunk size

### Chunk Size Trade-offs

```text
Smaller Chunks (128-256 tokens)        Larger Chunks (512-1024 tokens)
├── More precise retrieval             ├── More context per chunk
├── Less context per chunk             ├── May include irrelevant content
├── More chunks to search              ├── Fewer chunks to search
├── Better for factoid Q&A             ├── Better for summarization
└── Higher retrieval recall            └── Higher retrieval precision
```

## Embedding Models

### Model Comparison

| Model | Dimensions | Context | Strengths |
| ----- | ---------- | ------- | --------- |
| **OpenAI text-embedding-3-large** | 3072 | 8K | High quality, expensive |
| **OpenAI text-embedding-3-small** | 1536 | 8K | Good quality/cost ratio |
| **Cohere embed-v3** | 1024 | 512 | Multilingual, fast |
| **BGE-large** | 1024 | 512 | Open source, competitive |
| **E5-large-v2** | 1024 | 512 | Open source, instruction-tuned |
| **GTE-large** | 1024 | 512 | Alibaba, good for Chinese |
| **Sentence-BERT** | 768 | 512 | Classic, well-understood |

### Embedding Selection

```text
Need best quality, cost OK?
├── Yes → OpenAI text-embedding-3-large
└── No
    └── Need self-hosted/open source?
        ├── Yes → BGE-large or E5-large-v2
        └── No
            └── Need multilingual?
                ├── Yes → Cohere embed-v3
                └── No → OpenAI text-embedding-3-small
```

### Embedding Optimization

| Technique | Description | When to Use |
| --------- | ----------- | ----------- |
| **Matryoshka embeddings** | Truncatable to smaller dims | Memory-constrained |
| **Quantized embeddings** | INT8/binary embeddings | Large-scale search |
| **Instruction-tuned** | Prefix with task instruction | Specialized retrieval |
| **Fine-tuned embeddings** | Domain-specific training | Specialized domains |

## Retrieval Strategies

### Dense Retrieval (Semantic Search)

```text
Query: "How to deploy containers"
         │
         ▼
    ┌─────────┐
    │ Embed   │
    │ Query   │
    └─────────┘
         │
         ▼
    ┌─────────────────────────────────┐
    │ Vector Similarity Search        │
    │ (Cosine, Dot Product, L2)       │
    └─────────────────────────────────┘
         │
         ▼
    Top-K semantically similar chunks
```

### Sparse Retrieval (BM25/TF-IDF)

```text
Query: "Kubernetes pod deployment YAML"
         │
         ▼
    ┌─────────┐
    │Tokenize │
    │ + Score │
    └─────────┘
         │
         ▼
    ┌─────────────────────────────────┐
    │ BM25 Ranking                    │
    │ (Term frequency × IDF)          │
    └─────────────────────────────────┘
         │
         ▼
    Top-K lexically matching chunks
```

### Hybrid Search (Best of Both)

```text
Query ──┬──▶ Dense Search ──┬──▶ Fusion ──▶ Final Ranking
        │                   │      │
        └──▶ Sparse Search ─┘      │
                                   │
        Fusion Methods:            ▼
        • RRF (Reciprocal Rank Fusion)
        • Linear combination
        • Learned reranking
```

### Reciprocal Rank Fusion (RRF)

```text
RRF Score = Σ 1 / (k + rank_i)

Where:
- k = constant (typically 60)
- rank_i = rank in each retrieval result

Example:
Doc A: Dense rank=1, Sparse rank=5
RRF(A) = 1/(60+1) + 1/(60+5) = 0.0164 + 0.0154 = 0.0318

Doc B: Dense rank=3, Sparse rank=1
RRF(B) = 1/(60+3) + 1/(60+1) = 0.0159 + 0.0164 = 0.0323

Result: Doc B ranks higher (better combined relevance)
```

## Multi-Stage Retrieval

### Two-Stage Pipeline

```text
┌─────────────────────────────────────────────────────────┐
│ Stage 1: Recall (Fast, High Recall)                     │
│ • ANN search (HNSW, IVF)                                │
│ • Retrieve top-100 candidates                           │
│ • Latency: 10-50ms                                      │
└─────────────────────────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│ Stage 2: Rerank (Slow, High Precision)                  │
│ • Cross-encoder or LLM reranking                        │
│ • Score top-100 → return top-10                         │
│ • Latency: 100-500ms                                    │
└─────────────────────────────────────────────────────────┘
```

### Reranking Options

| Reranker | Latency | Quality | Cost |
| -------- | ------- | ------- | ---- |
| **Cross-encoder (local)** | Medium | High | Compute |
| **Cohere Rerank** | Fast | High | API cost |
| **LLM-based rerank** | Slow | Highest | High API cost |
| **BGE-reranker** | Fast | Good | Compute |

## Context Assembly

### Context Window Management

```text
Context Budget: 128K tokens
├── System prompt: 500 tokens (fixed)
├── Conversation history: 4K tokens (sliding window)
├── Retrieved context: 8K tokens (dynamic)
└── Generation buffer: ~115K tokens (available)

Strategy: Maximize retrieved context quality within budget
```

### Context Assembly Strategies

| Strategy | Description | When to Use |
| -------- | ----------- | ----------- |
| **Simple concatenation** | Join top-K chunks | Small context, simple Q&A |
| **Relevance-ordered** | Most relevant first | General retrieval |
| **Chronological** | Time-ordered | Temporal queries |
| **Hierarchical** | Summary + details | Long-form generation |
| **Interleaved** | Mix sources | Multi-source queries |

### Lost-in-the-Middle Problem

```text
LLM Attention Pattern:
┌─────────────────────────────────────────────────────────┐
│ Beginning           Middle            End               │
│    ████              ░░░░             ████              │
│  High attention   Low attention   High attention        │
└─────────────────────────────────────────────────────────┘

Mitigation:
1. Put most relevant at beginning AND end
2. Use shorter context windows when possible
3. Use hierarchical summarization
4. Fine-tune for long-context attention
```

## Advanced RAG Patterns

### Query Transformation

```text
Original Query: "Tell me about the project"
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
    ┌─────────┐      ┌──────────┐     ┌──────────┐
    │ HyDE    │      │ Query    │     │ Sub-query│
    │ (Hypo   │      │ Expansion│     │ Decomp.  │
    │ Doc)    │      │          │     │          │
    └─────────┘      └──────────┘     └──────────┘
         │                 │                 │
         ▼                 ▼                 ▼
    Hypothetical      "project,        "What is the
    answer to         goals,           project scope?"
    embed             timeline,        "What are the
                      deliverables"    deliverables?"
```

### HyDE (Hypothetical Document Embeddings)

```text
Query: "How does photosynthesis work?"
                │
                ▼
        ┌───────────────┐
        │ LLM generates │
        │ hypothetical  │
        │ answer        │
        └───────────────┘
                │
                ▼
"Photosynthesis is the process by which
plants convert sunlight into energy..."
                │
                ▼
        ┌───────────────┐
        │ Embed hypo    │
        │ document      │
        └───────────────┘
                │
                ▼
    Search with hypothetical embedding
    (Better matches actual documents)
```

### Self-RAG (Retrieval-Augmented LM with Self-Reflection)

```text
┌─────────────────────────────────────────────────────────┐
│ 1. Generate initial response                            │
│ 2. Decide: Need more retrieval? (critique token)        │
│    ├── Yes → Retrieve more, regenerate                  │
│    └── No → Check factuality (isRel, isSup tokens)      │
│ 3. Verify claims against sources                        │
│ 4. Regenerate if needed                                 │
│ 5. Return verified response                             │
└─────────────────────────────────────────────────────────┘
```

### Agentic RAG

```text
Query: "Compare Q3 revenue across regions"
                │
                ▼
        ┌───────────────┐
        │ Query Agent   │
        │ (Plan steps)  │
        └───────────────┘
                │
    ┌───────────┼───────────┐
    ▼           ▼           ▼
┌───────┐   ┌───────┐   ┌───────┐
│Search │   │Search │   │Search │
│ EMEA  │   │ APAC  │   │ AMER  │
│ docs  │   │ docs  │   │ docs  │
└───────┘   └───────┘   └───────┘
    │           │           │
    └───────────┼───────────┘
                ▼
        ┌───────────────┐
        │  Synthesize   │
        │  Comparison   │
        └───────────────┘
```

## Evaluation Metrics

### Retrieval Metrics

| Metric | Description | Target |
| ------ | ----------- | ------ |
| **Recall@K** | % relevant docs in top-K | >80% |
| **Precision@K** | % of top-K that are relevant | >60% |
| **MRR (Mean Reciprocal Rank)** | 1/rank of first relevant | >0.5 |
| **NDCG** | Graded relevance ranking | >0.7 |

### End-to-End Metrics

| Metric | Description | Target |
| ------ | ----------- | ------ |
| **Answer correctness** | Is the answer factually correct? | >90% |
| **Faithfulness** | Is the answer grounded in context? | >95% |
| **Answer relevance** | Does it answer the question? | >90% |
| **Context relevance** | Is retrieved context relevant? | >80% |

### Evaluation Framework

```text
┌─────────────────────────────────────────────────────────┐
│                RAG Evaluation Pipeline                  │
├─────────────────────────────────────────────────────────┤
│ 1. Query Set: Representative questions                  │
│ 2. Ground Truth: Expected answers + source docs         │
│ 3. Metrics:                                             │
│    • Retrieval: Recall@K, MRR, NDCG                     │
│    • Generation: Correctness, Faithfulness              │
│ 4. A/B Testing: Compare configurations                  │
│ 5. Error Analysis: Identify failure patterns            │
└─────────────────────────────────────────────────────────┘
```

## Common Failure Modes

| Failure Mode | Cause | Mitigation |
| ------------ | ----- | ---------- |
| **Retrieval miss** | Query-doc mismatch | Hybrid search, query expansion |
| **Wrong chunk** | Poor chunking | Better segmentation, overlap |
| **Hallucination** | Poor grounding | Faithfulness training, citations |
| **Lost context** | Long-context issues | Hierarchical, summarization |
| **Stale data** | Outdated index | Incremental updates, TTL |

## Scaling Considerations

### Index Scaling

| Scale | Approach |
| ----- | -------- |
| <1M docs | Single node, exact search |
| 1-10M docs | Single node, HNSW |
| 10-100M docs | Distributed, sharded |
| >100M docs | Distributed + aggressive filtering |

### Latency Budget

```text
Typical RAG Pipeline Latency:

Query embedding:     10-50ms
Vector search:       20-100ms
Reranking:          100-300ms
LLM generation:     500-2000ms
────────────────────────────
Total:              630-2450ms

Target p95: <3 seconds for interactive use
```

## Related Skills

- `llm-serving-patterns` - LLM inference infrastructure
- `vector-databases` - Vector store selection and optimization
- `ml-system-design` - End-to-end ML pipeline design
- `estimation-techniques` - Capacity planning for RAG systems

## Version History

- v1.0.0 (2025-12-26): Initial release - RAG architecture patterns for systems design

---

## Last Updated

**Date:** 2025-12-26

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
