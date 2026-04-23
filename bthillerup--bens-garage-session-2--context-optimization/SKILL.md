---
name: context-optimization
description: This skill should be used when the user asks to "optimize context", "reduce token costs", "improve context efficiency", "implement KV-cache optimization", "partition context", or mentions context limits, observation masking, or extending effective context capacity. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Context Optimization Techniques

Context optimization extends effective capacity through strategic compression, masking, caching, and partitioning. The goal is making better use of available capacity—effective optimization can double or triple effective context without requiring larger models.

## When to Activate

Activate this skill when:
- Context limits constrain task complexity
- Optimizing for cost reduction
- Reducing latency for long conversations
- Implementing long-running agent systems
- Building production systems at scale

## Core Strategies

### 1. Compaction
Summarize context contents when approaching limits, then reinitialize with the summary. First lever in context optimization.

**Priority for compression**:
1. Tool outputs (replace with summaries)
2. Old turns (summarize early conversation)
3. Retrieved docs (summarize if recent versions exist)
4. Never compress system prompt

### 2. Observation Masking
Tool outputs can comprise 80%+ of token usage. Replace verbose outputs with compact references once their purpose is served.

**Masking Strategy**:
- **Never mask**: Critical to current task, most recent turn, active reasoning
- **Consider masking**: 3+ turns ago, verbose with extractable key points
- **Always mask**: Repeated outputs, boilerplate, already summarized

### 3. KV-Cache Optimization
Cache Key and Value tensors computed during inference. Prefix caching reuses KV blocks across requests with identical prefixes.

**Optimization patterns**:
- Place stable elements first (system prompt, tool definitions)
- Then frequently reused elements
- Then unique elements last
- Avoid dynamic content like timestamps

### 4. Context Partitioning
Split work across sub-agents with isolated contexts. Each operates in clean context focused on its subtask without accumulated context from other subtasks.

## Budget Management

Design explicit context budgets:
- Allocate tokens by category: system prompt, tools, retrieved docs, history, buffer
- Monitor usage against budget
- Trigger optimization at 70-80% utilization

**Trigger-based signals**:
- Token utilization above 80%
- Degradation indicators
- Performance drops

## What to Apply When

| Context Composition | Optimization |
|---------------------|--------------|
| Tool outputs dominate | Observation masking |
| Retrieved docs dominate | Summarization or partitioning |
| Message history dominates | Compaction with summarization |
| Multiple components | Combine strategies |

## Performance Targets

- Compaction: 50-70% token reduction, <5% quality degradation
- Masking: 60-80% reduction in masked observations
- Cache optimization: 70%+ hit rate for stable workloads

## Guidelines

1. Measure before optimizing—know your current state
2. Apply compaction before masking when possible
3. Design for cache stability with consistent prompts
4. Partition before context becomes problematic
5. Monitor optimization effectiveness over time
6. Balance token savings against quality preservation
7. Implement graceful degradation for edge cases

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
