---
name: specswarm-build
description: Checks if build workflow should continue to next phase Use when this capability is needed.
metadata:
  author: martybonacci
---

# SpecSwarm Build Workflow

Provides natural language access to `/specswarm:build` command.

## Dynamic Context

Current build state:
`!cat .specswarm/build-loop.state 2>/dev/null || echo "No active build"`

## When to Invoke

Trigger this skill when the user mentions:
- Building, creating, or adding features
- Implementing or developing functionality
- Making or adding components
- Any request to build software features

**Examples:**
- "Build user authentication"
- "Create a payment system"
- "Add dashboard analytics"
- "Implement shopping cart"

## Instructions

**Skill-Based Routing:**

1. **Detect** that user mentioned building/creating software
2. **Extract** the feature description from their message
3. **Route based on intent clarity**:

   **Clear intent** - Execute directly:
   - Clear feature requests: "Please build a simple website", "Create user authentication with JWT", "Add dashboard analytics"
   - Action: Immediately run `/specswarm:build "feature description"`

   **Ambiguous intent** - Ask for confirmation:
   - Less specific: "Add authentication", "Work on the app"
   - Action: Use AskUserQuestion tool with two options:
     - Option 1 (label: "Run /specswarm:build"): Use SpecSwarm's complete workflow
     - Option 2 (label: "Process normally"): Handle as regular Claude Code request

4. **If user selects Option 2**, process normally without SpecSwarm
5. **After command completes**, STOP - do not continue with ship/merge

## What the Build Command Does

`/specswarm:build` runs complete workflow:
- Creates specification
- Asks clarifying questions
- Generates implementation plan
- Breaks down into tasks
- Implements all tasks
- Validates quality

Stops after implementation - does NOT merge/ship/deploy.

## Semantic Understanding

This skill should trigger not just on exact keywords, but semantic equivalents:

**Build equivalents**: build, create, make, develop, implement, add, construct, set up, establish, design
**Feature terms**: feature, component, functionality, module, system, page, form, interface

## Example

```
User: "Build user authentication with JWT"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/martybonacci) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
