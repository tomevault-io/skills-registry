---
name: agent-selector
description: Agent discovery and selection. Load when delegating tasks to specialist agents. Use when this capability is needed.
metadata:
  author: justin-delano
---

# Agent Selection

## Registry

Read `~/.claude/agents/registry.jsonl` for available specialists.

## Selection

Match task keywords to agent `capabilities` list.

When selected, read agent's `config` file and adopt its `system_prompt`.

## If No Match

Proceed without delegation or consult meta-generator at `~/.claude/meta/agent-generator/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justin-delano) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
