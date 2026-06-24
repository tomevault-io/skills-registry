---
name: docker-container-health-inspector
description: Inspects Docker container health using the Docker Engine API v1.45 /containers/{id}/json and /containers/{id}/stats endpoints. Diagnoses OOM kills, restart loops, and network connectivity issues with automated log analysis via /containers/{id}/logs streaming. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Docker Container Health Inspector

Inspects Docker container health using the Docker Engine API v1.45 /containers/{id}/json and /containers/{id}/stats endpoints. Diagnoses OOM kills, restart loops, and network connectivity issues with automated log analysis via /containers/{id}/logs streaming.

## Installation

Use the upstream install or setup path that matches your environment:
- Docker Engine releases are tagged with a **docker-** prefix (e.g. docker-v29.0.0 for Docker Engine 29.0.0).

Requirements and caveats from upstream:
- Moby is an open-source project created by Docker to enable and accelerate software containerization.
- ## Relationship with Docker
- The components and tools in the Moby Project are initially the open source components that Docker and the community have built for the Docker Project.

- Source: https://github.com/moby/moby
- Extracted from upstream docs: https://raw.githubusercontent.com/moby/moby/HEAD/README.md

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/docker-container-health-inspector/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
