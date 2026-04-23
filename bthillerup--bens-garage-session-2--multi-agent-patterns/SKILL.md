---
name: multi-agent-patterns
description: This skill should be used when the user asks to "design multi-agent system", "implement supervisor pattern", "create swarm architecture", "coordinate multiple agents", or mentions multi-agent patterns, context isolation, agent handoffs, or parallel agent execution. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Multi-Agent Architecture Patterns

Multi-agent architectures distribute work across multiple language model instances, each with its own context window. The critical insight is that **sub-agents exist primarily to isolate context**, not to anthropomorphize role division.

## When to Activate

Activate this skill when:
- Single-agent context limits constrain task complexity
- Tasks decompose naturally into parallel subtasks
- Different subtasks require different tool sets
- Building systems that must handle multiple domains simultaneously

## Why Multi-Agent

### The Context Bottleneck
Single agents face ceilings in reasoning capability, context management, and tool coordination. Multi-agent architectures partition work across multiple context windows.

### Token Economics

| Architecture | Token Multiplier | Use Case |
|--------------|------------------|----------|
| Single agent chat | 1× baseline | Simple queries |
| Single agent with tools | ~4× baseline | Tool-using tasks |
| Multi-agent system | ~15× baseline | Complex research/coordination |

**Key finding**: Model upgrades often provide larger gains than doubling token budgets.

## Architectural Patterns

### Pattern 1: Supervisor/Orchestrator
Central agent delegates to specialists and synthesizes results.

```
User Query -> Supervisor -> [Specialist, Specialist] -> Aggregation -> Output
```

**Advantages**: Strict control, easier human-in-the-loop, adheres to plans
**Disadvantages**: Supervisor context becomes bottleneck, "telephone game" problem

**The Telephone Game Solution**: Implement `forward_message` tool allowing sub-agents to pass responses directly to users.

### Pattern 2: Peer-to-Peer/Swarm
No central control. Agents communicate directly through explicit handoff mechanisms.

```python
def transfer_to_agent_b():
    return agent_b  # Handoff via function return
```

**Advantages**: No single point of failure, scales for breadth-first exploration
**Disadvantages**: Coordination complexity, risk of divergence

### Pattern 3: Hierarchical
Layers of abstraction: strategic, planning, and execution layers.

```
Strategy Layer -> Planning Layer -> Execution Layer
```

**Advantages**: Mirrors organizational structures, clear separation of concerns
**Disadvantages**: Coordination overhead between layers

## Context Isolation Mechanisms

**Full context delegation**: Sub-agent receives complete context for complex tasks
**Instruction passing**: Sub-agent receives only instructions for simple tasks
**File system memory**: Agents read/write to persistent storage, avoiding context bloat

## Consensus and Coordination

### Weighted Voting
Weight agent votes by confidence or expertise.

### Debate Protocols
Require agents to critique each other's outputs. Adversarial critique often yields higher accuracy than collaborative consensus.

### Trigger-Based Intervention
Monitor for: stall triggers (no progress), sycophancy triggers (agents mimic each other)

## Failure Modes and Mitigations

| Failure | Mitigation |
|---------|------------|
| Supervisor Bottleneck | Output schema constraints, checkpointing |
| Coordination Overhead | Minimize communication, batch results |
| Divergence | Clear objective boundaries, convergence checks |
| Error Propagation | Validate outputs before passing, retry logic |

## Guidelines

1. Design for context isolation as primary benefit
2. Choose pattern based on coordination needs, not organizational metaphor
3. Implement explicit handoff protocols with state passing
4. Use weighted voting or debate protocols for consensus
5. Monitor for supervisor bottlenecks
6. Set time-to-live limits to prevent infinite loops

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
