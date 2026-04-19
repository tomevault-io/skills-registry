---
name: artifact-selector
description: Intelligent artifact type selection for Claude Code workflows. Use when designing workflows, creating new artifacts, or deciding between commands, skills, hooks, and agents. Use when this capability is needed.
metadata:
  author: mit17k
---

# Artifact Selector

## When to Use This Skill

- Designing a new workflow
- Deciding what type of artifact to create
- Analyzing requirements to suggest artifact types
- Checking for redundancy with existing artifacts

## Decision Tree

Use this flowchart to select the optimal artifact type:

```
                    ┌──────────────────┐
                    │ What triggers    │
                    │ the action?      │
                    └────────┬─────────┘
                             │
        ┌────────────────────┼────────────────────┐
        ▼                    ▼                    ▼
   User types           Automatic            Every matching
   /command             context match        operation
        │                    │                    │
        ▼                    ▼                    ▼
   ┌─────────┐         ┌─────────┐         ┌─────────┐
   │ COMMAND │         │  SKILL  │         │  HOOK   │
   └─────────┘         └─────────┘         └─────────┘
        │                    │
        │              ┌─────┴─────┐
        │              ▼           ▼
        │         Background   Side effects
        │         knowledge    (deploy, etc)
        │              │           │
        │              ▼           ▼
        │         user-invocable  disable-model-
        │         : false         invocation: true
        │
        │
   Needs isolated    ─────────────────────────▶  ┌─────────┐
   context? Yes                                   │  AGENT  │
                                                  └─────────┘
```

## Keyword-to-Artifact Mapping

| Requirement Keywords | Suggested Artifact | Reasoning |
|---------------------|-------------------|-----------|
| "automatic", "when", "context", "background" | Skill | Claude auto-applies based on context |
| "explicit", "command", "user triggers", "/action" | Command | User explicitly invokes |
| "isolated", "parallel", "deep analysis", "separate" | Agent | Needs isolated context window |
| "every time", "on save", "after edit", "before run" | Hook | Triggers on tool lifecycle |
| "project-wide", "all sessions", "always remember" | CLAUDE.md | Ambient project context |

## Artifact Selection Matrix

### Choose COMMAND when:
- User should explicitly trigger (`/command-name`)
- Has observable side effects (deploy, commit, publish)
- Needs arguments from user
- One-time discrete action

**Examples:**
- `/deploy` - Deploy to production
- `/pr` - Create pull request
- `/test run` - Run specific tests

### Choose SKILL when:
- Claude should automatically apply knowledge
- Provides patterns, conventions, or domain expertise
- Background context that applies across tasks
- Reusable knowledge without side effects

**Examples:**
- API design patterns skill
- Security review checklist skill
- Testing best practices skill

### Choose AGENT when:
- Deep analysis requiring isolated context
- Parallel independent work
- Specialized expertise (security audit, code review)
- Task shouldn't pollute main conversation

**Examples:**
- Security audit agent
- Test generator agent
- Documentation agent

### Choose HOOK when:
- Action must run on every matching operation
- Automation (formatting, linting, logging)
- Guardrails (block dangerous commands)
- No user interaction needed

**Examples:**
- Auto-format after file write
- Security check before bash command
- Audit logging after modifications

### Choose CLAUDE.md when:
- Information applies to all sessions
- Project architecture and conventions
- Critical paths and gotchas
- Quick reference that's always relevant

**Examples:**
- Project structure overview
- Build and test commands
- Key architectural decisions

## Redundancy Detection

Before creating a new artifact, check:

1. **Scan existing commands:** `.claude/commands/**/*.md`
2. **Scan existing skills:** `.claude/skills/*/SKILL.md`
3. **Scan existing agents:** `.claude/agents/*.md`
4. **Check settings.json:** hooks already configured

### Signs of Redundancy:
- Similar trigger conditions in descriptions
- Overlapping functionality
- Same files/patterns being processed
- Duplicate validation logic

### When to Combine vs Separate:
- **Combine** if artifacts share >70% of logic
- **Separate** if they have distinct triggers or contexts
- **Extend** existing artifact if adding related functionality

## Parallel vs Sequential Execution

### Use Parallel Agents when:
- Tasks are independent
- No shared state needed
- Different analysis perspectives
- Speed is important

```
Task security-audit "Audit auth code" &
Task perf-review "Review performance" &
Task test-coverage "Check coverage"
```

### Use Sequential when:
- Output of one feeds into another
- Shared state required
- Order matters

## Token Budget Guide

| Artifact Type | Target | Maximum |
|---------------|--------|---------|
| Command | <100 tokens (simple), <300 (complex) | 500 |
| Skill description | <200 chars | 1024 chars |
| Skill SKILL.md | <2KB | Split to references/ |
| Agent | <500 tokens | 1000 |
| Hook script | N/A (external) | Keep under 200 lines |
| CLAUDE.md | <1000 tokens | 300 lines |

## Anti-Patterns

### ❌ Creating a command when a hook would work
If it should run automatically on every file save, use a hook.

### ❌ Creating an agent for simple tasks
Agents have overhead. Simple reads don't need isolated context.

### ❌ Putting everything in CLAUDE.md
CLAUDE.md is for ambient context, not workflows. Use skills for patterns.

### ❌ Duplicate logic across artifacts
Extract shared logic to a skill that multiple artifacts reference.

### ❌ Over-scoping tool access
Give agents/skills only the tools they need. Don't grant write access for read-only tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mit17k) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
