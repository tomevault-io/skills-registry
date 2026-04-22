---
name: openai-agents-sdk-expert
description: Specialist in the OpenAI Agents SDK, Swarm architectures, and lightweight multi-agent orchestration. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# OpenAI Agents SDK Expert

## Overview

This skill represents the elite expertise required to master the **OpenAI Agents SDK**. It focuses on building low-latency, lightweight multi-agent systems without the bloat of heavy frameworks. This expert understands the core primitives of "Agents" and "Handoffs" to create scalable, intelligent routing networks.

---

# Process

## 🚀 High-Level Workflow

### Phase 1: Agent Definition & Patterns
-   **Atomic Agent Design**: Defining single-purpose agents with strict instructions (system prompts).
-   **Handoff Orchestration**: Implementing `transfer_to_agent` functions to allow dynamic switching between agents (e.g., Triage -> Support -> Sales).
-   **Context Variables**: Managing state that persists and adapts as execution flows between agents.

### Phase 2: Tool Engineering
-   **Function Calling Mastery**: Writing side-effect-free, deterministic tools that agents can invoke reliably.
-   **Schema Optimization**: Crafting JSON schemas and docstrings that optimize model understanding and reduce hallucinations.
-   **Dependency Injection**: injecting context-aware dependencies into tools at runtime.

### Phase 3: Production & Evaluation
-   **Loop Management**: Controlling the `run_loop` to prevent infinite recursion or excessive token usage.
-   **Streaming Responses**: Implementing real-time streaming of agent thoughts and tool outputs for better UX.
-   **Eval-Driven Development**: Using datasets to strictly evaluate agent behavior and handoff accuracy.

---

# Reference Files

## 📚 Technologies & Concepts
-   **OpenAI Agents SDK**: The core library for building agentic workflows.
-   **Swarm Patterns**: Educational framework and patterns for lightweight multi-agent orchestration.
-   **Pydantic**: For strict data validation and schema generation.
-   **Network of Agents**: The architectural pattern of treating agents as nodes in a graph.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
