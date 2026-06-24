---
name: act-local-github-actions-runner
description: act is an open-source CLI tool that runs GitHub Actions workflows locally using Docker, enabling fast feedback on workflow changes without pushing to GitHub. With 57,000+ stars on GitHub, it is the standard tool for local Actions development and testing. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# act Local GitHub Actions Runner

act is an open-source CLI tool that runs GitHub Actions workflows locally using Docker, enabling fast feedback on workflow changes without pushing to GitHub. With 57,000+ stars on GitHub, it is the standard tool for local Actions development and testing.

## Installation

Use the upstream install or setup path that matches your environment:
- If you are using macOS, please be sure to follow the steps outlined in Docker Docs for how to install Docker Desktop for Mac .
- If you are using Linux, you will need to install Docker Engine .
- git clone https://github.com/nektos/act.git
- make build

Requirements and caveats from upstream:
- 4.2. Docker context
- Necessary prerequisites for running act
- act depends on docker (exactly Docker Engine API) to run workflows in containers. As long you don't require container isolation, you can run selected (e.g. windows or macOS) jobs directly on your System, see Runners ....

Basic usage or getting-started notes:
- Introduction
- Installation
- 2.1. Arch

- Source: https://github.com/nektos/act
- Extracted from upstream docs: https://nektosact.com/installation/index.html

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/act-local-github-actions-runner/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
