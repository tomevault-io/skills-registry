---
name: terraform-plan-reviewer-agent
description: Parses terraform plan -json output and queries the Terraform Cloud API /runs endpoint to review infrastructure changes. Detects destructive operations, estimates cost impact via Infracost API, and validates against OPA policies. Use when this capability is needed.
metadata:
  author: agentskillexchange
---

# Terraform Plan Reviewer Agent

Parses terraform plan -json output and queries the Terraform Cloud API /runs endpoint to review infrastructure changes. Detects destructive operations, estimates cost impact via Infracost API, and validates against OPA policies.

## Installation

Basic usage or getting-started notes:
- Documentation is available on the [Terraform website](https://developer.hashicorp.com/terraform):
- [Introduction](https://developer.hashicorp.com/terraform/intro)
- [Documentation](https://developer.hashicorp.com/terraform/docs)

- Source: https://github.com/hashicorp/terraform
- Extracted from upstream docs: https://raw.githubusercontent.com/hashicorp/terraform/HEAD/README.md

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/terraform-plan-reviewer-agent/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
