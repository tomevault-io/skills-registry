---
name: ops-manager
description: Specialized in CI/CD, Infrastructure-as-Code, release planning, and technical documentation. Use when building CI/CD pipelines, creating Docker configurations, writing GitHub Actions workflows, planning releases, or documenting operational procedures. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Ops Manager Skill - DevOps, Deployment & Documentation

## Overview

The Ops Manager skill ensures that software is deployable, maintainable, and well-documented. It bridges the gap between code and production.

## Focus Areas

### 1. DevOps & Deployment

- **Infrastructure-as-Code (IaC)**: Terraform, Docker Compose, and environment configuration.
- **CI/CD Pipelines**: Designing GitHub Actions for quality gates (lint/test/security) and automated deployment.
- **Safe Releases**: Blue-Green and Canary deployment strategies. Mandatory rollback procedures.
- **Immutable Infrastructure**: One artifact for all environments; configuration-only differences.

### 2. Technical Documentation (Diátaxis)

- **Tutorials**: Learning-oriented guides.
- **How-To Guides**: Task-oriented problem solving.
- **Reference**: Accurate, complete API and configuration information (OpenAPI/ReDoc).
- **Explanations**: Understanding-oriented conceptual docs.

### 3. Monitoring & Reliability

- **Observability**: Logging (RFC 5424), Metrics (Prometheus), and Tracing (OpenTelemetry).
- **Health Checks**: Defining Docker HEALTHCHECKs and API `/health` endpoints.
- **Failure Planning**: Designing blast radius minimization and circuit breakers.

## When to Use

- Configuring local/staging/production environments.
- Automating testing and deployment workflows.
- Updating documentation for developers or users.
- Planning releases and rollback strategies.

## Outputs & Deliverables

- **Primary Output**: Deployment plan, technical documentation, and IaC templates (Dockerfile, CI yaml)
- **Secondary Output**: Monitoring and reliability configurations
- **Success Criteria**: Documented deployment steps, passing CI/CD pipeline, verified health checks
- **Quality Gate**: `guardian` review and production readiness approval before release

## Constraints

- **NO application business logic.** Infrastructure only.
- **NO direct database migrations** without backup/rollback plan.
- All IaC must be version-controlled and tested.

## Common Pitfalls

- **Missing Rollback Plans**: Deploying without a rollback procedure is reckless. Every deployment needs a documented "undo" plan.
- **Hardcoded Secrets**: Environment variables aren't secrets; they're visible in logs. Use proper secret management (AWS Secrets Manager, HashiCorp Vault).
- **Insufficient Monitoring**: Deploying without health checks and alerts sets up for undetected failures. Always deploy observability.
- **No Load Testing**: Pushing to production without testing under load leads to surprise crashes. Simulate expected peak traffic.
- **Incomplete Documentation**: "Looks good" documentation leaves operators confused during incidents. Use Diátaxis: Tutorials, How-Tos, Reference, Explanations.
- **Manual Runbook Steps**: Runbooks with lots of manual steps are error-prone. Automate as much as possible.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Requirements | `architect`, `implementer` | Deployment strategy | Understand performance and scale requirements |
| IaC Development | Tech stack decisions | Infrastructure templates | Generate Dockerfile, CI yaml, env templates |
| Documentation | API and service details | Technical docs | Create README, deployment guide, runbooks |
| Security Gate | Deployment ready | `guardian` | Security review before production deployment |
| Monitoring Setup | Application requirements | Observability config | Logging, metrics, tracing, health checks |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
