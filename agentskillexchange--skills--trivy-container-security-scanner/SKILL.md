---
name: trivy-container-security-scanner
description: Integrates Aqua Security Trivy CLI for comprehensive container image vulnerability scanning. Detects OS package CVEs, language-specific dependency vulnerabilities, and IaC misconfigurations with SARIF output format for CI/CD pipeline integration.
metadata:
  author: agentskillexchange
---

# Trivy Container Security Scanner

Integrates Aqua Security Trivy CLI for comprehensive container image vulnerability scanning. Detects OS package CVEs, language-specific dependency vulnerabilities, and IaC misconfigurations with SARIF output format for CI/CD pipeline integration.

## Installation

Requirements and caveats from upstream:
- ![Docker Pulls][docker-pulls]
- docker run aquasec/trivy
- There are canary builds ([Docker Hub](https://hub.docker.com/r/aquasec/trivy/tags?page=1&name=canary), [GitHub](https://github.com/aquasecurity/trivy/pkgs/container/trivy/75776514?tag=canary), [ECR](https://gallery.ec...

Basic usage or getting-started notes:
- ### Get Trivy
- Trivy is available in most common distribution channels. The full list of installation options is available in the [Installation] page. Here are a few popular examples:
- brew install trivy

- Source: https://github.com/aquasecurity/trivy
- Extracted from upstream docs: https://raw.githubusercontent.com/aquasecurity/trivy/HEAD/README.md

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/trivy-container-security-scanner/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
