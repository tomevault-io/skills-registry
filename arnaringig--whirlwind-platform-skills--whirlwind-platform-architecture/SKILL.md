---
name: whirlwind-platform-architecture
description: Cross-repo architecture and change orchestration across the Whirlwind platform (Terragrunt control plane, Terraform infrastructure, networking DNS, and Ansible config). Use when designing or implementing platform-wide changes that span multiple repos. Use when this capability is needed.
metadata:
  author: arnaringig
---

# Whirlwind Platform Architecture

## Overview
Coordinate changes that span the full Whirlwind stack across Terragrunt, Terraform infrastructure, networking DNS, and Ansible configuration.

## Cross-repo workflow
1. Define the change goal and the impacted runtime surface.
2. Map the change to repos using `references/cross-repo-map.md`.
3. Identify required contracts (DNS-01, SSM parameters, secrets, IAM roles).
4. Sequence the changes so shared networking and infrastructure land before configuration.
5. Validate with targeted applies and one environment before scaling out.

## Common change patterns
- DNS-01 or hosted zone changes: update networking module, then Terragrunt stacks, then workloads.
- Ansible pipeline changes: update Terraform infra CI/CD, then Ansible repo variables and scripts.
- Compute changes (ASG or NLB): update Terraform infra modules, then Terragrunt wiring, then Ansible inventory strategy.

## References
- `references/cross-repo-map.md`
- `references/dns-01-architecture.md`
- `references/auto-scaling-plan.md`
- `references/operational-contracts.md`
- `references/runbook-dns-change.md`
- `references/runbook-ansible-pipeline-change.md`
- `references/runbook-autoscaling.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arnaringig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
