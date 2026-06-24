---
name: terraform-module
description: Inspect Terraform module specs from cache when seeing module sources. Use when this capability is needed.
metadata:
  author: ForceInjection
---

# Terraform module

## Workflow

1. Check if `.terraform/modules/` exists: `find . -type d -name ".terraform"`
2. Initialize if needed: `terraform init -backend=false`
3. Find module: `find .terraform/modules -type d -name "*<module-name>*"`
4. Read key files: `README.md`, `variables.tf`, `versions.tf`, `outputs.tf`
5. Fallback: Clone to `/tmp` if local inspection fails

**Note**: Support both hyphen and underscore conventions (`aws-s3`, `aws_s3`).
Prefer cached modules over cloning. Requires Terraform, Git, and credentials.

## Output

- Variable definitions (types, descriptions, defaults)
- Required Terraform/provider versions
- Module outputs and descriptions
- Documentation and usage examples
- Source and version information

---
> Source: [ForceInjection/domain-driven-design-skills](https://github.com/ForceInjection/domain-driven-design-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
