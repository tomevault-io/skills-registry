---
name: terraform-plan-review
description: Reviews Terraform plans and IaC diffs for risky infrastructure changes, destructive actions, security exposure, drift, and rollout concerns. Use when this capability is needed.
metadata:
  author: jakesterns
---

# Terraform Plan Review

Use this skill when reviewing Terraform, OpenTofu, or infrastructure-as-code plan output and related diffs.

## Inputs

- Plan output, IaC diff, module changes, variable changes, provider versions, and target environment
- Expected intent of the infrastructure change

## Steps

1. Summarize resource counts: create, update, replace, destroy, and no-op if available.
2. Identify destructive or replacement actions and whether they are expected.
3. Review changes to networking, IAM, secrets, encryption, logging, public exposure, backups, and data stores.
4. Check for drift or unexpected changes outside the stated intent.
5. Inspect module/provider/version changes for blast radius.
6. Note operational risks: downtime, state migration, dependency ordering, quotas, and rollback difficulty.
7. Recommend pre-apply checks and post-apply verification.

## Output

Return:

- Plan summary
- Expected changes
- Unexpected or risky changes
- Security and compliance concerns
- Apply readiness recommendation
- Verification checklist

## Guidelines

- Treat `destroy` and `replace` as high attention even when expected.
- Do not approve a plan if the intent is unclear.
- Include exact resource names when available.

---
> Source: [jakesterns/agent-skills](https://github.com/jakesterns/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
