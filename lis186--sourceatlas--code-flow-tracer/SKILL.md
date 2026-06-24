---
name: code-flow-tracer
description: Trace code execution paths and data flow. Use when user asks "how does X work", "what happens when X", "trace the flow of X", "where does data come from", or needs to understand feature implementation. Use when this capability is needed.
metadata:
  author: lis186
---

# Code Flow Tracer

## When to Use

Trigger this skill when the user:
- Wants to understand how a feature works end-to-end
- Asks what happens when an action is triggered
- Needs to trace data flow through the system
- Asks "how does X work"
- Asks "what calls X" or "what does X call"

## Instructions

1. Identify the feature, function, or flow the user wants to trace
2. Run `/sourceatlas:flow "<query>"` with a natural language description
3. Returns call graph, boundary detection, and flow visualization

## Query Formats

- Feature flow: `/sourceatlas:flow "user login"`
- Function trace: `/sourceatlas:flow "handleSubmit"`
- Error paths: `/sourceatlas:flow "error handling flow"`
- Data origin: `/sourceatlas:flow "where does userProfile come from"`
- Reverse trace: `/sourceatlas:flow "who calls validateToken"`

## What User Gets

- Call graph visualization (ASCII tree)
- Boundary detection (API, DB, LIB, CLOUD markers)
- Recursion and cycle detection
- Entry points identification
- 11 analysis modes available

## Example Triggers

- "How does the login flow work?"
- "What happens when user clicks submit?"
- "Trace the checkout process"
- "Where does this data come from?"
- "Who calls this function?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lis186) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
