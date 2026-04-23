---
name: memory-systems
description: This skill should be used when the user asks to "implement agent memory", "persist state across sessions", "build knowledge graph", "track entities", or mentions memory architecture, temporal knowledge graphs, vector stores, or cross-session persistence. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Memory System Design

Memory provides the persistence layer that allows agents to maintain continuity across sessions. Simple agents lose all state when sessions end. Sophisticated agents implement layered memory architectures.

## When to Activate

Activate this skill when:
- Building agents that must persist across sessions
- Needing to maintain entity consistency across conversations
- Implementing reasoning over accumulated knowledge
- Designing systems that learn from past interactions

## Memory Architecture Spectrum

### The Context-Memory Spectrum

| Layer | Latency | Persistence | Capacity |
|-------|---------|-------------|----------|
| Working Memory (context) | Zero | Volatile | Limited |
| Short-Term Memory | Low | Session | Moderate |
| Long-Term Memory | Medium | Cross-session | Large |
| Entity Memory | Medium | Permanent | Large |
| Temporal Knowledge Graph | High | Permanent | Unlimited |

### Why Simple Vector Stores Fall Short

Vector RAG loses relationship information. If an agent learns "Customer X purchased Product Y on Date Z," a vector store can retrieve this fact directly but cannot answer "What products did customers who purchased Product Y also buy?"

### Benchmark Performance

| Memory System | Accuracy | Latency |
|---------------|----------|---------|
| Zep (Temporal KG) | 94.8% | 2.58s |
| MemGPT | 93.4% | Variable |
| GraphRAG | ~75-85% | Variable |
| Vector RAG | ~60-70% | Fast |
| Recursive Summarization | 35.3% | Low |

## Memory Layers

### Layer 1: Working Memory
The context window itself. Zero-latency but vanishes when sessions end.

### Layer 2: Short-Term Memory
Session-scoped databases, file-system storage, in-memory caches.

### Layer 3: Long-Term Memory
Cross-session persistent storage. Implementations range from key-value stores to graph databases.

### Layer 4: Entity Memory
Specifically tracks entities (people, places, concepts) to maintain consistency. Creates rudimentary knowledge graph.

### Layer 5: Temporal Knowledge Graph
Facts have "valid from" and optionally "valid until" timestamps. Enables time-travel queries.

## Implementation Patterns

### Pattern 1: File-System-as-Memory
Simple, requires no additional infrastructure.

```
memory/
  entities/
    user_preferences.yaml
    project_decisions.md
  sessions/
    2026-01-09.md
```

### Pattern 2: Vector RAG with Metadata
Semantic search with filtering on entity tags, temporal validity, source attribution.

### Pattern 3: Knowledge Graph
Explicitly model entities and relationships.

### Pattern 4: Temporal Knowledge Graph
Add validity periods to facts:

```python
def query_at_time(entity_id, query_time):
    return graph.query("""
        MATCH (entity)-[r]->(value)
        WHERE entity.id = $entity_id
        AND r.valid_from <= $query_time
        AND (r.valid_until IS NULL OR r.valid_until > $query_time)
        RETURN value
    """)
```

## Memory Consolidation

Memories accumulate and require consolidation to prevent unbounded growth.

**Triggers**: Significant accumulation, too many outdated results, periodic schedule, explicit request

**Process**: Identify outdated facts, merge related facts, update validity periods, archive obsolete facts

## Guidelines

1. Match memory architecture to query requirements
2. Use temporal validity to prevent outdated information conflicts
3. Consolidate memories periodically
4. Design for memory retrieval failures gracefully
5. Consider privacy implications of persistent memory
6. Monitor memory growth and performance over time

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
