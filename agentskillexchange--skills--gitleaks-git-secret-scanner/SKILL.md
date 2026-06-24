---
name: gitleaks-git-repository-secret-scanner
description: Gitleaks is an open-source SAST tool for detecting hardcoded secrets like passwords, API keys, and tokens in Git repositories, files, and directories. With 24,000+ GitHub stars and 20 million Docker downloads, it is the most widely adopted open-source secret scanner. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Gitleaks Git Repository Secret Scanner

Gitleaks is an open-source SAST tool for detecting hardcoded secrets like passwords, API keys, and tokens in Git repositories, files, and directories. With 24,000+ GitHub stars and 20 million Docker downloads, it is the most widely adopted open-source secret scanner.

## Installation

Use the upstream install or setup path that matches your environment:
- brew install gitleaks
- docker pull zricethezav/gitleaks:latest
- docker run -v ${path_to_host_folder_to_scan}:/path zricethezav/gitleaks:latest [COMMAND] [OPTIONS] [SOURCE_PATH]
- docker pull ghcr.io/gitleaks/gitleaks:latest

Requirements and caveats from upstream:
- [dockerhub]: https://hub.docker.com/r/zricethezav/gitleaks
- [dockerhub-badge]: https://img.shields.io/docker/pulls/zricethezav/gitleaks.svg
- [![Docker Hub][dockerhub-badge]][dockerhub]

Basic usage or getting-started notes:
- ### Installing
- bash
- # MacOS

- Source: https://github.com/gitleaks/gitleaks
- Extracted from upstream docs: https://raw.githubusercontent.com/gitleaks/gitleaks/HEAD/README.md

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/gitleaks-git-secret-scanner/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
