---
name: agentdb-vector-search-optimization
description: Optimize AgentDB vector search performance using quantization for 4-32x memory reduction, HNSW indexing for 150x faster search, caching, and batch operations for scaling to millions of vectors. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# AgentDB Vector Search Optimization

## Overview

Optimize AgentDB performance with quantization (4-32x memory reduction), HNSW indexing (150x faster search), caching, and batch operations for scaling to millions of vectors.

## SOP Framework: 5-Phase Optimization

### Phase 1: Baseline Performance (1 hour)
- Measure current metrics (latency, throughput, memory)
- Identify bottlenecks
- Set optimization targets

### Phase 2: Apply Quantization (1-2 hours)
- Configure product quantization
- Train codebooks
- Apply compression
- Validate accuracy

### Phase 3: Implement HNSW Indexing (1-2 hours)
- Build HNSW index
- Tune parameters (M, efConstruction, efSearch)
- Benchmark speedup

### Phase 4: Configure Caching (1 hour)
- Implement query cache
- Set TTL and eviction policies
- Monitor hit rates

### Phase 5: Benchmark Results (1-2 hours)
- Run comprehensive benchmarks
- Compare before/after
- Validate improvements

## Quick Start

```typescript
import { AgentDB, Quantization, QueryCache } from 'agentdb-optimization';

const db = new AgentDB({ name: 'optimized-db', dimensions: 1536 });

// Quantization (4x memory reduction)
const quantizer = new Quantization({
  method: 'product-quantization',
  compressionRatio: 4
});
await db.applyQuantization(quantizer);

// HNSW indexing (150x speedup)
await db.createIndex({
  type: 'hnsw',
  params: { M: 16, efConstruction: 200 }
});

// Caching
db.setCache(new QueryCache({
  maxSize: 10000,
  ttl: 3600000
}));
```

## Optimization Techniques

### Quantization
- **Product Quantization**: 4-8x compression
- **Scalar Quantization**: 2-4x compression
- **Binary Quantization**: 32x compression

### Indexing
- **HNSW**: 150x faster, high accuracy
- **IVF**: Fast, partitioned search
- **LSH**: Approximate search

### Caching
- **Query Cache**: LRU eviction
- **Result Cache**: TTL-based
- **Embedding Cache**: Reuse embeddings

## Success Metrics

- Memory reduction: 4-32x
- Search speedup: 150x
- Accuracy maintained: > 95%
- Cache hit rate: > 70%

## Additional Resources

- Full docs: SKILL.md
- AgentDB Optimization: https://agentdb.dev/docs/optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
