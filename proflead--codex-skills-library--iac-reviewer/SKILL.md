---
name: iac-reviewer
description: Review infrastructure-as-code changes for safety and correctness. Use when a mid-level developer needs a second look on IaC. Use when this capability is needed.
metadata:
  author: proflead
---

# IaC Reviewer

## Purpose
Review infrastructure-as-code changes for safety and correctness.

## Inputs to request
- IaC plan output or diff.
- Target environments and accounts.
- Rollback or drift policy.

## Workflow
1. Check resource changes for drift and deletion risk.
2. Validate security groups, IAM, and networking rules.
3. Confirm plan/apply order and state handling.

## Output
- IaC review findings with risks.

## Quality bar
- Flag destructive changes clearly.
- Confirm least-privilege IAM changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proflead) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
