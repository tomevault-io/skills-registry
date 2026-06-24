---
name: infra-terraform
description: Terraform / IaC skill. Covers modularity, state management, variables and environments, drift detection, least privilege, plan and review, and rollback and splitting. Used for designing or reviewing infrastructure code. Use when this capability is needed.
metadata:
  author: OriPoin
---

# Terraform / IaC

## When to Use

- Standing up new cloud infrastructure; converting hand-managed resources to IaC; multi-environment governance.
- Collaboration protocol: see [ops-agent-collaboration](../ops-agent-collaboration/SKILL.md).

## Golden Rules

1. **Modular**: reusable modules are versioned; business code composes them as root modules.
2. **Remote state + locking**: remote backend + locking; never share local state across people.
3. **Environment isolation**: dev / staging / prod each have their own state; never share one state across environments.
4. **Plan must be reviewed**: review the plan before apply; CI runs plan and outputs the diff.
5. **Least-privilege IAM**: the IaC execution role is authorized only within the change scope.
6. **Drift detection**: regularly run `terraform plan` to see drift versus reality.
7. **Rollback path**: either revert PR + apply, or restore from snapshot; never hand-fix.

## Routines

### Routine A: Repository Layout

```
modules/
  network/
  database/
envs/
  dev/main.tf
  staging/main.tf
  prod/main.tf
state backend: s3 + dynamodb lock
```

### Routine B: Release Flow

```
PR modifies envs/staging
-> CI runs fmt + validate + plan
-> review plan output
-> merge + auto apply (staging)
prod requires a separate manual apply trigger
```

### Routine C: Drift Monitoring

```
Daily scheduled plan per env; alert if output is non-empty
Manual reconciliation: either bring IaC in line or revert reality
```

## Anti-patterns

### [BAD] Multiple people editing local state

Fix: remote backend + locking.

### [BAD] Manually changing resources in the console

Problem: drift from code. Fix: all changes go through IaC.

### [BAD] One state containing multiple environments

Fix: separate state per env.

### [BAD] Applying without reviewing the plan

Fix: CI plan output must be reviewed.

### [BAD] IaC role with full admin

Fix: least privilege, scoped per module.

## Review Checklist

- Modular + remote state + locking?
- Each environment with its own state?
- Plan included in review?
- Drift detection in place?
- IAM minimal?
- Rollback path clear?

---
> Source: [OriPoin/AgentOrch](https://github.com/OriPoin/AgentOrch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
