---
name: infrastructure-setup
description: Standards for AWS ECS+EC2, Terraform, Parameter Store, and GitHub Actions Use when this capability is needed.
metadata:
  author: ishankgulati
---

# Infrastructure Setup Guidelines

## Core Technology Stack
- **Cloud Provider**: AWS
- **Compute**: ECS (Elastic Container Service) using **EC2 Launch Type** (preferred over Fargate for this user).
- **Infrastructure as Code**: Terraform.
- **Secrets Management**: AWS Systems Manager Parameter Store.
- **CI/CD**: GitHub Actions.

## Terraform Guidelines
- Use modular structure for reusable components.
- Store state remotely (e.g., S3 backend).
- Retrieve all secrets and sensitive configuration from **AWS SSM Parameter Store**. Do not fallback to local `.env` files for production.

## ECS & EC2 Configuration
- Prefer ECS on EC2 for cost/performance control.
- Ensure autoscaling is configured for both the ECS Services and the underlying EC2 Auto Scaling Groups.

## Deployment Workflow
- All deployments must be automated via **GitHub Actions**.
- Workflows should include:
  1. Linting & Testing (Terraform fmt/validate, Go test).
  2. Building Docker images.
  3. Pushing to ECR.
  4. Updating ECS Services (force new deployment).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ishankgulati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
