---
name: terraform-patterns
description: Use when writing, reviewing, or planning Terraform infrastructure code. Covers module design, state management (S3+DynamoDB), workspace strategy, variable validation, drift detection, and CI/CD integration with GitHub Actions.
metadata:
  author: bipinks
---

# Terraform Patterns

## Project Structure
```
infrastructure/
├── modules/                 # Reusable modules
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   └── README.md
│   ├── compute/
│   ├── database/
│   └── monitoring/
├── environments/            # Environment-specific configs
│   ├── dev/
│   │   ├── main.tf
│   │   ├── backend.tf
│   │   ├── terraform.tfvars
│   │   └── providers.tf
│   ├── staging/
│   └── production/
├── global/                  # Shared resources (IAM, DNS)
└── scripts/
    ├── plan.sh
    └── apply.sh
```

## State Management
```hcl
# backend.tf — Remote state with S3 + DynamoDB locking
terraform {
  backend "s3" {
    bucket         = "myorg-terraform-state"
    key            = "production/networking/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
    kms_key_id     = "alias/terraform-state"
  }
}
```

## Module Design
```hcl
# modules/networking/variables.tf
variable "vpc_cidr" {
  description = "CIDR block for the VPC"
  type        = string
  validation {
    condition     = can(cidrhost(var.vpc_cidr, 0))
    error_message = "Must be a valid CIDR block."
  }
}

variable "environment" {
  description = "Environment name"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "production"], var.environment)
    error_message = "Must be dev, staging, or production."
  }
}

variable "tags" {
  description = "Common tags for all resources"
  type        = map(string)
  default     = {}
}
```

## Resource Tagging Convention
```hcl
locals {
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
    ManagedBy   = "terraform"
    Owner       = var.team_name
    CostCenter  = var.cost_center
    CreatedAt   = timestamp()
  }
}
```

## Best Practices
1. **Remote state** — Always use encrypted remote state with locking
2. **Modules** — Create reusable modules for common patterns
3. **Variables** — Add validation rules and descriptions to all variables
4. **State isolation** — Separate state per environment and component
5. **Provider versioning** — Pin provider versions with `~>` constraints
6. **Plan before apply** — Always review plans, never auto-apply for production
7. **Drift detection** — Run `terraform plan` on schedule to detect drift
8. **Import existing** — Use `terraform import` for brownfield resources
9. **Workspaces** — Use for minor variations; separate configs for major differences
10. **Data sources** — Reference existing resources instead of hardcoding IDs

## CI/CD Integration
```yaml
# GitHub Actions — Terraform plan on PR, apply on merge
- name: Terraform Plan
  run: |
    terraform init -backend-config=env/${{ env.ENVIRONMENT }}/backend.hcl
    terraform plan -var-file=env/${{ env.ENVIRONMENT }}/terraform.tfvars -out=tfplan
    terraform show -json tfplan > plan.json

- name: Post Plan to PR
  uses: actions/github-script@v7
  with:
    script: |
      // Post plan output as PR comment
```

## Anti-Patterns to Avoid
- ❌ Hardcoded resource IDs
- ❌ Using `latest` AMIs without data sources
- ❌ Storing secrets in `.tfvars` files
- ❌ Single monolithic state file
- ❌ Manual console changes alongside Terraform
- ❌ Ignoring provider version constraints

---
> Source: [bipinks/ghost-office](https://github.com/bipinks/ghost-office) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
