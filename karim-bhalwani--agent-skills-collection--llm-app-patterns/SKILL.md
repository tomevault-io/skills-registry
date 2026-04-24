---
name: llm-app-patterns
description: Production LLM application patterns, architectures, and best practices. Covers RAG pipelines, agent architectures, prompt engineering, LLMOps, and production deployment patterns. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# LLM Application Patterns

Expert in production LLM application patterns and architectures.

## When to Use This Skill

Use when:

- Building production RAG (Retrieval-Augmented Generation) pipelines
- Implementing AI agents with tool use and multi-step reasoning
- Designing prompt engineering strategies and template systems
- Setting up LLMOps: monitoring, logging, tracing, and evaluation
- Deploying LLM applications with caching, rate limiting, and fallbacks
- Choosing between different agent architectures (ReAct, function calling, plan-execute, multi-agent)
- Optimizing retrieval: chunking strategies, vector databases, hybrid search
- Building production-ready systems: cost optimization, reliability, observability

---

## Core Capabilities

This skill provides production-proven patterns for:

1. **RAG Pipelines** - Document ingestion, chunking, embedding, retrieval, generation
2. **Agent Architectures** - ReAct, function calling, plan-execute, multi-agent collaboration
3. **Prompt Engineering** - Templates, versioning, A/B testing, chaining
4. **LLMOps & Monitoring** - Metrics, logging, tracing, evaluation frameworks
5. **Production Patterns** - Caching, rate limiting, retry logic, fallbacks

---

## Pattern References

For detailed implementation guidance, see:

### [RAG Pipelines](references/rag-pipelines.md)

**Use when:** Building search-augmented LLM applications

Covers:

- Document ingestion and preprocessing
- Chunking strategies (fixed, semantic, sliding window)
- Vector database selection and configuration
- Retrieval patterns (dense, sparse, hybrid, multi-vector)
- Generation with retrieved context

### [Agent Architectures](references/agent-architectures.md)

**Use when:** Building agents that use tools or multi-step reasoning

Covers:

- ReAct pattern (Reasoning + Acting)
- Function calling for structured tool use
- Plan-and-execute for complex tasks
- Multi-agent collaboration patterns
- Architecture decision matrix

### [Prompt Engineering](references/prompt-engineering.md)

**Use when:** Creating reusable prompt systems

Covers:

- Prompt templates with variables
- Versioning and A/B testing
- Prompt chaining for multi-step workflows
- Few-shot learning patterns
- Best practices for prompt structure

### [LLMOps & Observability](references/llmops-observability.md)

**Use when:** Setting up monitoring and evaluation

Covers:

- Key metrics to track (performance, quality, cost, reliability)
- Logging and distributed tracing
- Evaluation frameworks and benchmarking
- Caching strategies for cost reduction
- Rate limiting and retry patterns
- Fallback strategies for reliability

---

## Quick Decision Guide

| Goal | Reference |
| :--- | :-------- |
| Answer questions from your docs | [RAG Pipelines](references/rag-pipelines.md) |
| Build tool-using agent | [Agent Architectures](references/agent-architectures.md) |
| Create reusable prompts | [Prompt Engineering](references/prompt-engineering.md) |
| Monitor production system | [LLMOps & Observability](references/llmops-observability.md) |

---

## Dependencies

- **architect** - For overall system design and architecture decisions
- **data-modeler** - For data schema design in RAG pipelines
- **ops-manager** - For production deployment and operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
