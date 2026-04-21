---
name: tf-apply
description: Apply Terraform changes to provision Azure infrastructure Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I do

Apply Terraform configuration to create, update, or delete Azure resources:
- Execute the planned changes
- Update the state file
- Output resource information

## When to use me

Use this skill when:
- Ready to provision infrastructure after reviewing plan
- Deploying new resources to Azure
- Updating existing infrastructure
- Running automated deployments

## Commands

```bash
# Interactive apply (prompts for confirmation)
terraform apply

# Apply a saved plan (no confirmation needed)
terraform apply tfplan

# Auto-approve (use with caution)
terraform apply -auto-approve

# Target specific resources
terraform apply -target=azurerm_resource_group.main

# With variable overrides
terraform apply -var="environment=prod"

# Replace a specific resource
terraform apply -replace=azurerm_virtual_machine.main
```

## Safety Guidelines

1. **Always review the plan first** - Never blind apply
2. **Use saved plans in production** - `terraform plan -out=tfplan && terraform apply tfplan`
3. **Avoid -auto-approve in production** - Manual confirmation prevents accidents
4. **Check state after apply** - Run `terraform show` to verify
5. **Have backups** - Ensure state file is backed up before major changes

## Common Issues

1. **Resource already exists**: Import with `terraform import`
2. **Timeout errors**: Check Azure resource provisioning times
3. **Permission denied**: Verify Azure RBAC permissions
4. **State lock**: Another process may be running, or lock needs manual release

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
