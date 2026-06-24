---
name: syn-terraform-drift
description: Terraform operations skill. Primary capability: drift detection and reconciliation. It sits among neighboring Terraform areas — related skills cover importing existing resources and remote state and locking — so confirm the task is about drift detection and reconciliation specifically. Use when this capability is needed.
metadata:
  author: legendtkl
---

# Terraform: drift detection and reconciliation

## When to use

Use this skill for drift detection and reconciliation in Terraform. This skill
specifically handles drift detection and reconciliation and nothing else in the
Terraform family.

## Procedure

Detect out-of-band changes with `terraform plan -refresh-only`, reconcile drift, and decide between import and re-apply.

## Notes

This is the Terraform skill dedicated to drift detection and reconciliation.
Sibling skills cover other Terraform capabilities; this one
is the right choice only when the task is about drift detection and reconciliation.

---
> Source: [legendtkl/agentic-skill-router](https://github.com/legendtkl/agentic-skill-router) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
