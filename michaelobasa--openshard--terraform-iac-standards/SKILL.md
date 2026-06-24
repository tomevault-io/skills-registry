---
name: terraform-iac-standards
description: Conventions and safety checks for Terraform infrastructure changes Use when this capability is needed.
metadata:
  author: MichaelObasa
---

Always run terraform plan before apply; never apply without reviewing the diff.
Pin provider versions in required_providers — avoid >= without an upper bound.
Store state remotely (S3 + DynamoDB or Terraform Cloud); never commit .tfstate.
Use variable files (.tfvars) for environment-specific values, not hardcoded strings.
Tag all resources with environment, owner, and cost-centre for billing visibility.

---
> Source: [MichaelObasa/openshard](https://github.com/MichaelObasa/openshard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
