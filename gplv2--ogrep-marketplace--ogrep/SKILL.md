---
name: ogrep
description: | Use when this capability is needed.
metadata:
  author: gplv2
---

## Mandatory: Use the Search Agent

**YOU MUST dispatch the ogrep-search agent for any semantic code search.**

Announce: "Dispatching ogrep search agent to find [topic]."

Then use the Agent tool with `subagent_type: "ogrep-search"`:

```
Agent tool:
  description: "Search codebase for [topic]"
  prompt: "Search for [specific query]. Focus on [what you're looking for - e.g., implementation details, architecture, data flow, error handling]."
  subagent_type: "ogrep-search"
```

The agent will:
1. Run `--summarize` for a cheap file-level overview
2. Narrow to the most relevant files
3. Expand specific chunks for evidence
4. Return synthesized findings with file:line references

**Saves context vs. running ogrep commands directly in your conversation.**

## When to Use

**Use ogrep when:**
- "Where is error handling done?"
- "How does caching work here?"
- "What validates user input?"
- Exploring unfamiliar code
- User asks a conceptual question about the codebase
- You'd need to guess multiple terms for grep

**Use grep/Glob instead when:**
- You know the exact class/function name (`class ErrorHandler`)
- Looking for specific imports or string literals
- Simple identifier searches

**Rule of thumb:** If you'd need to guess multiple terms for grep, dispatch the ogrep agent.

## Direct CLI Access (Discouraged)

You CAN run `ogrep` commands directly via Bash, but DON'T — it wastes your context window with raw JSON output. Always dispatch the agent instead.

Exception: `ogrep index .` for first-time indexing can be run directly if the agent reports no index exists.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gplv2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
