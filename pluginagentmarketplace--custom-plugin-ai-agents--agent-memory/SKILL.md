---
name: agent-memory
description: Implement agent memory - short-term, long-term, semantic storage, and retrieval Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Agent Memory

Give agents the ability to remember and learn across conversations.

## When to Use This Skill

Invoke this skill when:
- Adding conversation history
- Implementing long-term memory
- Building personalized agents
- Managing context windows

## Parameter Schema

| Parameter | Type | Required | Description | Default |
|-----------|------|----------|-------------|---------|
| `task` | string | Yes | Memory goal | - |
| `memory_type` | enum | No | `buffer`, `summary`, `vector`, `hybrid` | `hybrid` |
| `persistence` | enum | No | `session`, `user`, `global` | `session` |

## Quick Start

```python
from langchain.memory import ConversationBufferWindowMemory

# Simple buffer (last k messages)
memory = ConversationBufferWindowMemory(k=10)

# With summarization
from langchain.memory import ConversationSummaryBufferMemory
memory = ConversationSummaryBufferMemory(llm=llm, max_token_limit=2000)

# Vector store memory
from langchain.memory import VectorStoreRetrieverMemory
memory = VectorStoreRetrieverMemory(retriever=vectorstore.as_retriever())
```

## Memory Types

| Type | Use Case | Pros | Cons |
|------|----------|------|------|
| Buffer | Short chats | Simple | No compression |
| Summary | Long chats | Compact | Loses detail |
| Vector | Semantic recall | Relevant | Slower |
| Hybrid | Production | Best of all | Complex |

## Multi-Layer Architecture

```python
class ProductionMemory:
    def __init__(self):
        self.short_term = BufferMemory(k=10)    # Recent
        self.summary = SummaryMemory()           # Compressed
        self.long_term = VectorMemory()          # Semantic
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Context overflow | Add summarization |
| Slow retrieval | Cache, reduce k |
| Irrelevant recall | Improve embeddings |
| Memory not persisting | Check storage backend |

## Best Practices

- Use multi-layer memory for production
- Set token limits to prevent overflow
- Add metadata (timestamps, importance)
- Implement TTL for old memories

## Related Skills

- `rag-systems` - Vector retrieval
- `llm-integration` - Context management
- `ai-agent-basics` - Agent architecture

## References

- [LangChain Memory](https://python.langchain.com/docs/concepts/#memory)
- [MemGPT Paper](https://arxiv.org/abs/2310.08560)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
