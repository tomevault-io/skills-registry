---
name: context-fundamentals
description: This skill should be used when the user asks to "understand context", "explain context windows", "design agent architecture", "debug context issues", "optimize context usage", or discusses context components, attention mechanics, progressive disclosure, or context budgeting. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Context Engineering Fundamentals

Context is the complete state available to a language model at inference time. It includes everything the model can attend to when generating responses: system instructions, tool definitions, retrieved documents, message history, and tool outputs.

## When to Activate

Activate this skill when:
- Designing new agent systems or modifying existing architectures
- Debugging unexpected agent behavior that may relate to context
- Optimizing context usage to reduce token costs or improve performance
- Onboarding new team members to context engineering concepts

## Core Concepts

### The Anatomy of Context

**System Prompts**
Establish the agent's core identity, constraints, and behavioral guidelines. Should be extremely clear with simple, direct language at the right altitude—specific enough to guide behavior, flexible enough for strong heuristics.

**Tool Definitions**
Specify actions an agent can take. Each tool includes name, description, parameters, and return format. Tool descriptions collectively steer agent behavior.

**Retrieved Documents**
Provide domain-specific knowledge. Use retrieval augmented generation (RAG) to pull relevant documents at runtime rather than pre-loading everything.

**Message History**
Contains conversation between user and agent. Can grow to dominate context usage in long-running tasks.

**Tool Outputs**
Results of agent actions: file contents, search results, API responses. Can reach 83.9% of total context usage.

### Context Windows and Attention Mechanics

**The Attention Budget Constraint**
For n tokens, attention creates n² relationships. As context grows, the model's ability to capture these relationships gets stretched thin. Models have an "attention budget" that depletes as context grows.

**The Progressive Disclosure Principle**
Load information only as needed. At startup, load only skill names and descriptions. Full content loads only when activated. This keeps agents fast while giving access to more context on demand.

### Context Quality vs Quantity

The assumption that larger context windows solve memory problems has been debunked. Context engineering means finding the **smallest possible set of high-signal tokens** that maximize the likelihood of desired outcomes.

**Guiding principle**: Informativity over exhaustiveness. Include what matters, exclude what doesn't, design for on-demand access.

## Practical Guidance

### File-System-Based Access
Store reference materials externally. Load files only when needed. The file system provides structure agents can navigate.

### Hybrid Strategies
Pre-load some context for speed (like CLAUDE.md files), but enable autonomous exploration for additional context as needed.

### Context Budgeting
- Know the effective context limit
- Monitor context usage during development
- Implement compaction triggers at 70-80% utilization
- Place critical information at attention-favored positions (beginning and end)

## Guidelines

1. Treat context as a finite resource with diminishing returns
2. Place critical information at beginning or end of context
3. Use progressive disclosure to defer loading until needed
4. Organize system prompts with clear section boundaries
5. Monitor context usage during development
6. Design for context degradation rather than hoping to avoid it
7. Prefer smaller high-signal context over larger low-signal context

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
