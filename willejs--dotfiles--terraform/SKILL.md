---
name: terraform
description: Terraform patterns for infrastructure as code, including resource management, modules, and state handling. Use when working with Terraform configurations. Use when this capability is needed.
metadata:
  author: willejs
---

# Terraform Skill

When this skill is activated, apply these Terraform best practices and patterns. Follow the [Google best practices](https://docs.cloud.google.com/docs/terraform/best-practices/general-style-structure) and [HashiCorp style guide](https://developer.hashicorp.com/terraform/language/style) when writing Terraform code.

## When activated

1. **Read existing code first**: Always examine current Terraform files to understand patterns and structure before making changes
2. **Check for existing modules**: Look for reusable modules in the `/modules` or component-specific module directories
3. **Verify provider versions**: Ensure provider versions are locked and `.terraform.lock.hcl` is present
4. **Follow existing naming conventions**: Match the casing and naming patterns already established in the codebase

## Code Style and Structure

### Naming Conventions
- Use `kebab-case` for resource names (e.g., `aws_instance.web-server`)
- Use `snake_case` for variable names, locals, and outputs (e.g., `instance_type`)
- Name resources descriptively to indicate their purpose (e.g., `aws_s3_bucket.log-storage` not `aws_s3_bucket.bucket1`)

### File Organization
- Place related resources in the same file when possible
- Use clear file names: `main.tf` (resources), `variables.tf` (inputs), `outputs.tf` (outputs), `versions.tf` (provider requirements), `locals.tf` (local values)
- Follow directory structure for components and environments:
  ```
  components/
    networking/
      prod/
        main.tf
      modules/
        vpc/
    compute/
      prod/
  modules/  # Shared modules across components
    vpc/
  ```

### Variables and Outputs
- Always set explicit types on variables (e.g., `type = string`, `type = map(string)`)
- Add descriptions to all variables explaining their purpose
- Add validation blocks where appropriate to catch errors early
- Export whole objects/maps from modules instead of individual attributes when suitable for flexibility
- Avoid using `sensitive` on outputs unless absolutely necessary
- Name outputs clearly, reflecting the attribute they expose, optionally prefixed with component name

Example:
```hcl
variable "instance_type" {
  description = "EC2 instance type for web servers"
  type        = string
  default     = "t3.micro"

  validation {
    condition     = can(regex("^t3\\.", var.instance_type))
    error_message = "Instance type must be from the t3 family."
  }
}
```

## Resource Management

### Resource Configuration
- Prefix all resource names with a namespace local (overridable by variable) to avoid collisions:
  ```hcl
  locals {
    namespace = var.namespace_override != "" ? var.namespace_override : "myapp"
  }

  resource "aws_s3_bucket" "data" {
    bucket = "${local.namespace}-data-bucket"
  }
  ```

### Tagging
- Add tags to all resources that support them
- Use a local map for common tags:
  ```hcl
  locals {
    common_tags = {
      Component   = "networking"
      ManagedBy   = "terraform"
    }
  }

  resource "aws_vpc" "main" {
    tags = local.common_tags
  }
  ```

### Dynamic Resources
- Use `for_each` for creating multiple similar resources (allows targeting specific instances)
- Use `count` for conditional resource creation (0 or 1)
- Use data sources to reference existing infrastructure instead of hardcoding values
- Select resources by attributes like name or tags, not by IDs

### Comments
- Only add comments that explain WHY something is done a certain way
- Document complex logic, non-obvious constraints, or workarounds
- Do not add redundant comments that just repeat what the code does

## Modules

### Module Design
- Create modules to encapsulate and reuse configurations
- Design modules to allow dependency injection for testing
- Keep modules focused on a single responsibility
- Place component-specific modules in `components/<component>/modules/`
- Place shared modules in top-level `/modules/`

### Module Usage
- Always specify module versions when using external modules
- Pass configuration through variables rather than hardcoding

## State Management

- Use remote state backends (S3 + DynamoDB, Terraform Cloud, etc.)
- Do NOT use workspaces for environment separation (use directories instead)
- Avoid storing secrets in state; use external secret management (AWS Secrets Manager, Vault, etc.)
- Include state backend configuration in `backend.tf` or `versions.tf`

## Testing

- Write automated tests using Terratest in a `test/` folder
- Test modules with different input combinations
- Verify outputs match expected values
- Test resource creation and destruction

## Version Control

- Always version lock providers using `required_providers` block
- Include `.terraform.lock.hcl` in version control
- Use semantic versioning constraints appropriately (e.g., `~> 5.0` for minor version updates)

Example:
```hcl
terraform {
  required_version = ">= 1.6"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

## Security

- Never hardcode credentials or secrets in Terraform code
- Use data sources or external secret management to retrieve sensitive values
- Mark sensitive variables with `sensitive = true`
- Use least-privilege IAM policies
- Enable encryption at rest and in transit where supported 

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willejs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
