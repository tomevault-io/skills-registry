---
name: tf-init
description: Initialize Terraform working directory and download Azure provider plugins Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I do

Initialize a Terraform working directory by:
- Downloading the Azure provider plugins
- Setting up the backend for state management
- Preparing the working directory for other commands

## When to use me

Use this skill when:
- Starting a new Terraform project
- After cloning a Terraform repository
- After modifying provider requirements
- When .terraform directory is missing or corrupted

## Commands

```bash
# Standard initialization
terraform init

# Reconfigure backend
terraform init -reconfigure

# Upgrade providers to latest versions
terraform init -upgrade

# Initialize with specific backend config
terraform init -backend-config="storage_account_name=mystorageaccount"
```

## Prerequisites

- Azure CLI installed and logged in (`az login`)
- Terraform CLI installed (>= 1.0.0)
- Valid providers.tf with Azure provider configuration

## Troubleshooting

1. **Provider download fails**: Check network connectivity and proxy settings
2. **Backend initialization fails**: Verify Azure Storage Account exists and credentials are valid
3. **Lock file issues**: Delete `.terraform.lock.hcl` and run `terraform init` again

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
