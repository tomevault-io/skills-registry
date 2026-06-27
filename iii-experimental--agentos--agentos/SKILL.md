---
name: orchestrate
description: Plan and execute multi-agent work — decompose features, spawn workers, monitor progress Use when this capability is needed.
metadata:
  author: iii-experimental
---

Use AgentOS orchestrator to break down complex tasks:

1. Plan: `curl -X POST http://localhost:3111/api/orchestrator/plan -H 'Content-Type: application/json' -d '{"description": "<task>"}'`
2. Execute: `curl -X POST http://localhost:3111/api/orchestrator/execute -H 'Content-Type: application/json' -d '{"planId": "<id>"}'`
3. Status: `curl -X POST http://localhost:3111/api/orchestrator/status -H 'Content-Type: application/json' -d '{"planId": "<id>"}'`

The orchestrator decomposes tasks into subtasks with hierarchical IDs, spawns workers for each leaf task, and monitors progress with lifecycle reactions.

---
> Source: [iii-experimental/agentos](https://github.com/iii-experimental/agentos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
