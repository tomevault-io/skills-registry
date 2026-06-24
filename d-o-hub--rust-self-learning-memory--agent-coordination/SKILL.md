---
name: agent-coordination
description: Coordinate multiple specialized Skills and Task Agents through parallel, sequential, swarm, hybrid, or iterative execution strategies. Use when orchestrating multi-worker workflows, managing dependencies, or optimizing complex task execution with quality gates. Use when this capability is needed.
metadata:
  author: d-o-hub
---

# Agent Coordination

Coordinate multiple specialized Skills and Task Agents through strategic execution patterns.

## Quick Reference

- **[Strategies](./strategies.md)** - Parallel, sequential, swarm, hybrid, iterative
- **[Skills vs Agents](./skills-agents.md)** - When to use each
- **[Quality Gates](./quality-gates.md)** - Validation checkpoints
- **[Examples](./examples.md)** - Coordination examples

## When to Use

- Orchestrating multi-worker workflows
- Managing dependencies between tasks
- Optimizing complex task execution
- Quality-critical work with validation

## CRITICAL: Skills vs Task Agents

**Skills** (via `Skill` tool): Instruction sets that guide Claude
- Examples: rust-code-quality, architecture-validation, plan-gap-analysis

**Agents** (via `Task` tool): Autonomous sub-processes that execute
- Examples: code-reviewer, test-runner, debugger, loop-agent

See **[Strategies](./strategies.md)** for coordination patterns and **[Skills vs Agents](./skills-agents.md)** for when to use each.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/d-o-hub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
