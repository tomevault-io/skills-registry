---
name: inspect-claude-code-multi-agent-runs-with-agents-observe
description: Gives Claude Code operators a live dashboard for multi-agent sessions, tool calls, file activity, and nested task progress so debugging starts from what the agents are actually doing. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Inspect Claude Code multi-agent runs with Agents Observe

Gives Claude Code operators a live dashboard for multi-agent sessions, tool calls, file activity, and nested task progress so debugging starts from what the agents are actually doing.

## Prerequisites

Claude Code, Docker, Node.js, and a local browser. The plugin runs a local server/container and captures Claude Code hook events for dashboard inspection.

## Installation

Use the upstream install or setup path that matches your environment:
- git clone https://github.com/simple10/agents-observe.git agents-observe
- brew install just
- docker-compose.yml # Container orchestration - not used by the plugin

Requirements and caveats from upstream:
- [Docker](https://www.docker.com/) (required — the server runs as a container)
- [Node.js](https://nodejs.org/) (required — hook scripts run via node)
- If docker or node are not installed on your host, the plugin will fail to properly load.

Basic usage or getting-started notes:
- bash
- # Add this repo as a marketplace
- claude plugin marketplace add simple10/agents-observe

- Source: https://github.com/simple10/agents-observe
- Extracted from upstream docs: https://raw.githubusercontent.com/simple10/agents-observe/HEAD/README.md

## Documentation

- https://github.com/simple10/agents-observe#readme

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/agents-observe-claude-code-observability/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
