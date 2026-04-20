---
name: terraform-code-review
description: Reviews Terraform HCL code for best practices, security, and infrastructure patterns. Use when this capability is needed.
metadata:
  author: pluto-atom-4
---

# Terraform Code Review Skill

**Purpose**: Review Terraform HCL code focusing on best practices, security configurations, and infrastructure patterns.

**Location**: `/terraform`

**Triggers**: When reviewing HCL files in the `terraform/` directory or any infrastructure modifications.

## Overview

This skill provides comprehensive code review of Terraform HCL files, focusing on best practices, security configurations, and state management patterns.

## Quick Commands

```bash
# Validate syntax
cd terraform && terraform validate

# Format code
terraform fmt -recursive

# Plan changes
terraform plan -out=tfplan

# Check for security issues
tfsec .

# Full review workflow
terraform validate && terraform fmt -recursive && tfsec .
```

## Review Focus Areas

### 1. HCL Best Practices

- **Naming Conventions**: Verify resources follow snake_case naming patterns
- **Resource Organization**: Ensure logical grouping and clear dependency relationships
- **Variable Defaults**: Check for appropriate defaults and type constraints
- **Code Formatting**: Validate consistent indentation (2 spaces) and structure
- **Comments & Documentation**: Ensure critical configurations are documented
- **DRY Principle**: Identify opportunities to use variables, locals, and modules to reduce duplication
- **Output Values**: Verify meaningful outputs for resource references and cross-stack usage

### 2. Security Groups & Network Configuration

- **Ingress Rules**:
  - Flag overly permissive rules (0.0.0.0/0, ::/0)
  - Validate specific port ranges and protocols
  - Recommend principle of least privilege
  - Check for unnecessary protocol exposure
  
- **Egress Rules**:
  - Verify explicit egress rules instead of default-allow-all
  - Recommend restricting outbound traffic to necessary destinations
  - Flag rules allowing access to sensitive services
  
- **Security Best Practices**:
  - Recommend security group descriptions for audit trails
  - Suggest using variables for CIDR blocks (environment-specific)
  - Validate no hardcoded sensitive data or credentials
  - Check for mutual security group references

### 3. State Management

- **State Files**:
  - Verify backend configuration for remote state (S3, Terraform Cloud)
  - Check for state encryption and access controls
  - Recommend locking mechanisms to prevent concurrent modifications
  - Validate state isolation by environment/project
  
- **Sensitive Data**:
  - Identify outputs containing sensitive information
  - Recommend using `sensitive = true` on sensitive outputs
  - Check for credentials in state files or configurations
  
- **State Consistency**:
  - Verify resource import strategies for existing infrastructure
  - Check state file backup and recovery procedures
  - Recommend workspace usage for environment separation

## Review Checklist

See [review-checklist.md](./review-checklist.md) for comprehensive checklists covering pre-review, configuration, security, state management, and performance & scalability considerations.

## Common Issues & Recommendations

### Issue: Overly Permissive Security Groups

```hcl
# ❌ BAD
ingress {
  from_port   = 0
  to_port     = 65535
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}
```

```hcl
# ✅ GOOD
ingress {
  from_port   = 443
  to_port     = 443
  protocol    = "tcp"
  cidr_blocks = var.allowed_cidrs
  description = "HTTPS from approved sources"
}
```

### Issue: Hardcoded Values

```hcl
# ❌ BAD
resource "aws_security_group" "main" {
  vpc_id = "vpc-12345678"
}
```

```hcl
# ✅ GOOD
resource "aws_security_group" "main" {
  vpc_id = var.vpc_id
}
```

### Issue: Missing State Backend

```hcl
# ❌ BAD - Local state (not recommended for teams)
# No terraform block defined

# ✅ GOOD
terraform {
  required_version = ">= 1.0"
  
  backend "s3" {
    bucket         = "terraform-state-prod"
    key            = "vpc/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

### Issue: Sensitive Data in Outputs

```hcl
# ❌ BAD
output "database_password" {
  value = aws_db_instance.main.password
}

# ✅ GOOD
output "database_endpoint" {
  value       = aws_db_instance.main.endpoint
  sensitive   = false
  description = "Database endpoint for connections"
}
```

## Review Process

1. **Syntax Validation**: Run `terraform validate` and check for errors
2. **Format Check**: Run `terraform fmt` to ensure consistent formatting
3. **Security Scan**: Use `tfsec` or similar tools to identify security issues
4. **Best Practices**: Review against this checklist
5. **Documentation**: Verify all resources and variables are documented
6. **Testing**: Recommend `terraform plan` output review before apply

## Tools & Commands

```bash
# Validate syntax
terraform validate

# Format code
terraform fmt -recursive

# Security scanning
tfsec .

# Plan with output
terraform plan -out=tfplan

# Graph visualization
terraform graph | dot -Tsvg > graph.svg
```

## Files in This Skill

- **SKILL.md** - This file (skill definition)
- **terraform/main.tf** - Primary resource definitions
- **terraform/variables.tf** - Input variables and their constraints
- **terraform/outputs.tf** - Output values for cross-stack references

## Integration with Development

- Code review skill used for:
  - Pre-commit validation during development
  - PR review before infrastructure changes
  - Security scanning before applying changes
  - Documentation and consistency checks
  - Onboarding new infrastructure developers

## Related Files

- [terraform/main.tf](../../../terraform/main.tf) - Infrastructure resources
- [terraform/variables.tf](../../../terraform/variables.tf) - Input variables
- [terraform/outputs.tf](../../../terraform/outputs.tf) - Output definitions
- [CLAUDE.md](../../CLAUDE.md) - Main project guidance

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluto-atom-4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
