---
name: building-agentic-systems
description: > Use when this capability is needed.
metadata:
  author: diegouis
---

# Building Agentic Systems

Comprehensive skill for building, orchestrating, and optimizing AI agent systems using Claude Code.

## Overview

This skill covers the full lifecycle of agentic engineering: from designing individual agents to orchestrating complex multi-agent workflows, building MCP servers, creating plugins, and evaluating agent systems.

## When Invoked Without Clear Intent

**MANDATORY**: You MUST call the `AskUserQuestion` tool — do NOT render these options as text:

AskUserQuestion(
  header: "Agentic",
  question: "What agentic engineering task do you need help with?",
  options: [
    { label: "Create Components", description: "Build agents, skills, commands, hooks, plugins, or MCP servers" },
    { label: "Multi-Agent Orchestration", description: "Agent teams, autonomous loops, context engineering, spec-driven dev" },
    { label: "Prompt Engineering", description: "Prompt design, trust ladder, agent evaluation, thinking models" },
    { label: "Workflow Factory", description: "Template-driven artifact creation with complexity assessment" }
  ]
)

If the user selects "Other", present: Quality Checklists, Error Handling Patterns, Inter-Artifact Contracts, Report Types.

## Reference Routing

> **CONTEXT GUARD**: Load reference files only when the user's request matches a specific topic below. Do NOT load all references upfront.

| User Intent | Reference File |
|---|---|
| Creating agents, skills, commands, hooks, plugins, or MCP servers | `references/component-architecture.md` |
| Multi-agent orchestration, autonomous coding loops, context engineering, agent teams, spec-driven dev | `references/orchestration-patterns.md` |
| Prompt engineering, context engineering, trust ladder, agent evaluation, thinking models | `references/prompt-engineering.md` |
| Step-by-step workflows for creating agents, skills, commands, hooks, plugins, MCP servers | `references/workflow-patterns.md` |
| Quality checklists, AWOS architecture, confirmation patterns, Composio SDK, decision matrix | `references/quality-checklists.md` |
| Artifact frontmatter schemas, expert systems, agent teams, headless automation | `references/artifacts.md` |
| Complexity assessment (Simple/Medium/Complex) for artifact depth | `references/complexity.md` |
| Inter-artifact contracts, producer-consumer data passing | `references/contracts.md` |
| Sprint contracts, quality gates, hard thresholds, evaluator calibration | `references/sprint-contracts.md` |
| Context reset, compaction, handoff artifacts, long-running tasks, context anxiety | `references/context-management.md` |
| Scaffolding audit, model capability evolution, harness component lifecycle | `references/scaffolding-lifecycle.md` |
| Generator-evaluator loop, iterate to completion, convergence detection | `commands/modes/iterate-to-completion.md` |
| Error handling patterns (Gate Guard, Step Fallback, Graceful Degradation, Pipeline Abort, Retry) | `references/error-handling.md` |
| Report type selection (Summary, YAML, Diff, Progress, Handoff, Comparison, Audit, Path-Only) | `references/report-types.md` |
| File naming, variable naming, placement rules | `references/naming-conventions.md` |
| Body templates for commands, skills, agents, hooks, expert systems | `templates/` directory |

## Visual Diagramming with Excalidraw

Use the Excalidraw MCP server to generate agent orchestration flows, MCP server architecture diagrams, plugin component maps, and fan-out/fan-in pattern visualizations. Describe what you need in natural language.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diegouis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
