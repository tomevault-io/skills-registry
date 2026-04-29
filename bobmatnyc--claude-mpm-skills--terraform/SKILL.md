---
name: terraform
description: Terraform infrastructure-as-code workflow patterns: state and environments, module design, safe plan/apply, drift control, and CI guardrails Use when this capability is needed.
metadata:
  author: bobmatnyc
---

# Terraform

## Quick Start (workflow)

```bash
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

## Safety Checklist

- State: remote backend + locking; separate state per environment
- Reviews: plan in CI; apply from a trusted runner with approvals
- Guardrails: `prevent_destroy` and policy checks for prod

## Load Next (References)

- `references/state-and-environments.md` — backends, locking, workspaces vs separate state, drift
- `references/modules-and-composition.md` — module interfaces, versioning, composition patterns
- `references/workflows-and-guardrails.md` — CI plan/apply, policy-as-code, safe migrations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobmatnyc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
