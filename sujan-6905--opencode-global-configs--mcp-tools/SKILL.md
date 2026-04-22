---
name: mcp-tools
description: MCP usage skill for documentation lookup, implementation search, and coordination with plugin-backed tools Use when this capability is needed.
metadata:
  author: sujan-6905
---

# MCP Tools Skill

## Purpose

Use MCP tools to reduce guesswork, not to pad context.

## Use This Skill For

- official documentation lookup with `context7`
- implementation pattern search with `gh_grep`
- coordinating MCP research with plugin-backed tools such as Supermemory

## Do Not Use This Skill For

- replacing direct codebase reading when local files answer the question
- broad, low-signal searches without a concrete target
- storing secrets or sensitive credentials in memory tools

## Tool Selection

### Use `context7` for

- official framework and library docs
- API signatures and supported options
- upgrade notes and version-specific behavior

### Use `gh_grep` for

- real-world implementation patterns
- examples that official docs do not spell out well
- cross-checking how production repos solve a problem

### Plugin coordination

- Treat Supermemory as a plugin-backed tool, not an MCP server, in this repository.
- Use MCP research tools to improve correctness, then use plugin-backed tools when the task needs durable memory or context injection.

## Research Workflow

1. Start with the narrowest possible doc query.
2. Pull only the section needed for the current task.
3. Use `gh_grep` only if docs leave uncertainty.
4. Use plugin-backed tools only for the parts they are actually responsible for.
5. Translate findings into the local codebase style rather than copying blindly.

## Rules

- Prefer docs before examples.
- Do not query broad topics when a specific API or feature is known.
- Avoid repeated searches for the same fact.
- Do not describe plugin-backed tools as MCP servers if they are configured differently.
- Summarize the relevant conclusion in your work, not the entire doc page.

## Done Criteria

- research materially improved correctness
- findings were applied, not merely collected
- unnecessary context bloat was avoided

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sujan-6905) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
