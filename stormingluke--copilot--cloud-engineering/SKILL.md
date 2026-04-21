---
name: cloud-engineering
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## General Cloud Principles
- Infrastructure as Code for everything — no manual console changes
- Least privilege IAM — use roles with minimal permissions
- Tag all resources: `environment`, `team`, `project`, `cost-centre`
- Enable audit logging on all accounts/subscriptions/projects
- Use managed services over self-hosted where operationally sensible
- Multi-AZ / multi-zone by default for production workloads

## Azure
- Use Managed Identities (workload identity) — never service principal secrets
- Resource groups per environment per service
- AKS: use workload identity federation, not pod identity (deprecated)
- Key Vault for secrets, integrated with workload identity
- Azure Policy for governance guardrails
- Naming convention: `{project}-{env}-{resource}-{region}`

## AWS
- Use IAM Roles for Service Accounts (IRSA) on EKS
- SSM Parameter Store or Secrets Manager for secrets
- VPC per environment, private subnets for workloads
- Use AWS Organizations with SCPs for guardrails
- EKS: prefer managed node groups or Karpenter for autoscaling
- Naming convention: `{project}-{env}-{resource}-{region}`

## GCP
- Use Workload Identity Federation for GKE
- Secret Manager for secrets
- Shared VPC for network isolation
- Organisation policies for governance
- GKE Autopilot for reduced operational overhead
- Naming convention: `{project}-{env}-{resource}-{region}`

## Terraform
- Module structure: `modules/{name}/main.tf,variables.tf,outputs.tf`
- Remote state in cloud storage with state locking
- Use `terraform plan` in CI, `terraform apply` only in CD
- Pin provider versions in `required_providers`
- Use data sources to reference existing resources, not hardcoded IDs
- Separate state files per environment

## Crossplane
- Compositions for platform team abstractions
- Claims for developer self-service
- ProviderConfigs with workload identity, never static credentials
- XRDs with sensible defaults and CEL validation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
