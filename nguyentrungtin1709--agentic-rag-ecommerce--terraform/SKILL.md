---
name: terraform
description: Terraform best practices for writing modular, secure, and maintainable infrastructure as code - use when creating or reviewing Terraform projects Use when this capability is needed.
metadata:
  author: nguyentrungtin1709
---

# Terraform Skill

Apply these guidelines when writing, reviewing, or scaffolding Terraform infrastructure code.

Use this skill when:
- A user asks to create or scaffold a Terraform project or module
- You are reviewing existing Terraform code for quality, security, or conventions
- A user asks about Terraform naming, tagging, variable management, or state management

---

## 1. Module Structure

Every module must contain exactly three files:

- `main.tf` — resource definitions
- `variables.tf` — input variable declarations
- `outputs.tf` — output value declarations

Place new AWS resources inside an existing module if one covers that service.
If no suitable module exists, create a new one. Never define resources in the
root module directly unless they do not logically belong to any child module.

---

## 2. Configuration Management

### No Hard-Coding

Never hard-code environment-specific values such as AMI IDs, instance types,
CIDR blocks, bucket names, or region strings. Define them as variables.

### Environment Files

Use `.tfvars` files per environment:

```
environments/
  development.tfvars
  staging.tfvars
  production.tfvars
```

Never commit `.tfvars` files that contain sensitive values to version control.
Add them to `.gitignore`.

### Variable Management Principles

- Declare all variables used by child modules in the root module as well
  (single source of truth).
- Do not set default values inside child modules.
- Set default values in the root module only, to centralize configuration.
- Provide sensible defaults where appropriate. Document each default clearly
  in `variables.tf` — what it is and why that value was chosen.

---

## 3. Security

- Never commit secrets, passwords, API keys, or private keys to Git.
- Use AWS Systems Manager Parameter Store or AWS Secrets Manager to store
  sensitive values; retrieve them in Terraform via `aws_ssm_parameter` data
  sources.
- Apply the Principle of Least Privilege to all IAM roles and policies:
  grant only the permissions required to perform the task, nothing more.

---

## 4. State Management

- Always use a remote backend (S3 + DynamoDB for state locking) instead of
  local state.
- Configure the backend in a dedicated `backend.tf` or `versions.tf` file.
- Never commit `terraform.tfstate` or `terraform.tfstate.backup` files.

Example backend configuration:

```hcl
terraform {
  backend "s3" {
    bucket         = "<state-bucket-name>"
    key            = "<project>/<environment>/terraform.tfstate"
    region         = "<aws-region>"
    dynamodb_table = "<lock-table-name>"
    encrypt        = true
  }
}
```

---

## 5. Code Quality

- Run `terraform fmt` before every commit.
- Run `terraform validate` to catch syntax and reference errors.
- Use `terraform plan` and review output before applying any change.
- Keep modules focused: one module per AWS service or logical unit.

---

## 6. Naming Convention

All AWS resource names must follow this format:

```
${var.project_name}-${var.environment}-<specific-name>
```

- `project_name` — short identifier for the project (e.g., `pod-stylist`)
- `environment` — deployment tier (e.g., `development`, `staging`, `production`)
- `specific-name` — concise, descriptive suffix (e.g., `s3-media-bucket`, `api-repository`)

Examples:
- S3 bucket: `pod-stylist-development-s3-media-bucket`
- Security group: `pod-stylist-production-server-sg`
- IAM role: `pod-stylist-staging-ecs-task-role`

### SSM Parameter Naming

Three groups, in order of scope:

**1. Global flat parameters** — shared across all projects and environments:
```
<PROJECT_PREFIX>_<PARAMETER_NAME>
```
Example: `MYAPP_AWS_REGION`, `MYAPP_PROJECT_NAME`

**2. Shared generic parameters** — scoped to a project and environment, not a specific app:
```
/${var.project_name}/${var.environment}/generic/<PARAMETER_NAME>
```
Example: `/<project>/development/generic/PRIMARY_DOMAIN_NAME`

**3. Application-scoped hierarchical parameters**:
```
/${var.project_name}/${var.environment}/<application>/<group>/<PARAMETER_NAME>
```
- `<application>` — e.g., `api`, `worker`, `admin`
- `<group>` — e.g., `database`, `storage`, `iam`, `redis`, `generic`
- `<PARAMETER_NAME>` — uppercase, e.g., `DB_PASSWORD`

Example: `/<project>/production/api/database/DB_PASSWORD`

---

## 7. Tagging Convention

Every resource must include these four tags:

```hcl
tags = {
  Name        = "${var.project_name}-${var.environment}-<specific-name>"
  Project     = var.project_name
  Environment = var.environment
  Terraform   = var.use_terraform
}
```

Rules:
- Apply `tags` to every resource, or pass a `tags` map into submodules.
- `var.use_terraform` is a boolean; it renders as `"true"` or `"false"` in AWS.
- Use consistent casing for tag keys across all modules.

---

## 8. Variable Naming Convention

Name Terraform variables using the pattern `aws_<service>_<attribute>` in snake_case:

- `aws` — cloud provider prefix (enables future `gcp_`, `azure_` namespacing)
- `<service>` — AWS service name: `vpc`, `ec2`, `rds`, `s3`, `ecr`, `iam`, `cloudwatch`
- `<attribute>` — short, meaningful attribute name

Examples:
- `aws_s3_bucket_name`
- `aws_ec2_instance_type`
- `aws_rds_max_allocated_storage`
- `aws_vpc_public_subnets_cidr`

---

## 9. Documentation

- Add a `README.md` to every new module describing its purpose, inputs,
  outputs, and a usage example.
- Update the root `README.md` when adding a new module or changing the
  overall structure.

---

## Review Checklist

- [ ] Each module has `main.tf`, `variables.tf`, `outputs.tf`
- [ ] No hard-coded values — all environment-specific values are variables
- [ ] No default values in child modules
- [ ] `.tfvars` files with secrets are excluded from Git
- [ ] Remote backend configured with state locking
- [ ] All IAM policies follow Principle of Least Privilege
- [ ] No secrets in source code — SSM Parameter Store or Secrets Manager used
- [ ] All resources follow `${project_name}-${environment}-<specific-name>` naming
- [ ] All resources have the four required tags
- [ ] Variables follow `aws_<service>_<attribute>` naming convention
- [ ] `terraform fmt` applied — no formatting differences
- [ ] `terraform validate` passes
- [ ] Module README exists and is up to date

---
> Source: [nguyentrungtin1709/agentic-rag-ecommerce](https://github.com/nguyentrungtin1709/agentic-rag-ecommerce) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
