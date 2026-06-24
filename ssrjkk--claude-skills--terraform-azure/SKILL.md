---
name: terraform-azure
description: Provisions Azure infrastructure using Terraform modules for compute, networking, and managed services. Use for Azure IaC. Use when this capability is needed.
metadata:
  author: ssrjkk
---
# Terraform Azure

> Infrastructure as code for Microsoft Azure.

## Quick Start
```hcl
provider "azurerm" { features {} }
resource "azurerm_resource_group" "main" { name = "my-resources"; location = "West Europe" }
resource "azurerm_linux_web_app" "app" {
  name = "my-webapp"; resource_group_name = azurerm_resource_group.main.name
  location = azurerm_resource_group.main.location; service_plan_id = azurerm_service_plan.main.id
  site_config { application_stack { node_version = "20-lts" } }
}
```

## When to Use
- Azure resource provisioning
- Multi-cloud infrastructure
- Enterprise compliance
- Repeatable environments

## Validation
1. terraform plan shows expected resources
2. Azure resources create successfully
3. terraform destroy cleans up properly

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
