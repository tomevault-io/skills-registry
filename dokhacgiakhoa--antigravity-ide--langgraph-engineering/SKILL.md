---
name: langgraph-engineering
description: Building stateful, resilient AI agents with LangGraph v1.0. Use when this capability is needed.
metadata:
  author: dokhacgiakhoa
---

# LangGraph Agent Engineering

> **Goal**: Build complex, multi-step AI workflows that are reliable, debuggable, and capable of long-running operations.

## 1. Core Concepts (The Graph)

- **State**: A explicitly defined schema (TypedDict/Pydantic) that tracks the agent's memory snapshot.
- **Nodes**: Functions that perform work (call LLM, run tool, modify state).
- **Edges**: Logic that routes flow between nodes (Conditional edges based on LLM output).

## 2. Architecture Patterns

## 🧠 Knowledge Modules (Fractal Skills)

### 1. [A. The ReAct Agent (Standard)](./sub-skills/a-the-react-agent-standard.md)
### 2. [B. Plan-and-Execute (Advanced)](./sub-skills/b-plan-and-execute-advanced.md)
### 3. [C. Human-in-the-Loop](./sub-skills/c-human-in-the-loop.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dokhacgiakhoa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
