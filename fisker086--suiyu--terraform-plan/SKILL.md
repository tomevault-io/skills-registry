---
name: terraform-plan
description: Analyze Terraform .tf files locally: list resources, check config, preview changes Use when this capability is needed.
metadata:
  author: fisker086
---

# Terraform Plan Skill

Provides read-only local Terraform file analysis:
- Scan .tf files and list all resources to be created
- Show providers and modules used
- Validate HCL syntax
- Summarize infrastructure changes

Use `builtin_terraform_plan` tool with fields:
- `operation`: one of "resources", "providers", "modules", "validate", "summary"
- `path`: path to terraform directory (default: current directory)

Note: Does NOT require terraform CLI installed. Parses .tf files directly.
All operations are read-only.

---
> Source: [fisker086/SuiYu](https://github.com/fisker086/SuiYu) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
