---
name: tf-validate
description: Validate Terraform configuration syntax and consistency Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I do

Validate Terraform configuration for:
- Syntax errors
- Internal consistency
- Variable validation rules
- Provider configuration issues

## When to use me

Use this skill when:
- After writing new Terraform code
- Before committing changes
- Debugging configuration errors
- As part of CI/CD pipeline

## Commands

```bash
# Validate configuration
terraform validate

# Format check (without changes)
terraform fmt -check

# Format all files
terraform fmt -recursive

# Validate with JSON output
terraform validate -json
```

## Validation Workflow

```bash
# Recommended validation workflow
terraform fmt -recursive
terraform init -backend=false  # Skip backend for faster validation
terraform validate
```

## Common Validation Errors

1. **Missing required argument**
   - Check resource documentation for required fields

2. **Unknown attribute**
   - Verify attribute name spelling
   - Check provider version compatibility

3. **Invalid reference**
   - Ensure referenced resource exists
   - Check for typos in resource names

4. **Type mismatch**
   - Verify variable types match expected values

## Additional Validation Tools

```bash
# TFLint - Enhanced linting
tflint

# Checkov - Security scanning
checkov -d .

# TFSec - Security analysis
tfsec .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
