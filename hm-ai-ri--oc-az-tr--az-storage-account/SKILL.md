---
name: az-storage-account
description: Create and configure Azure Storage Accounts with Terraform Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I do

Help create and manage Azure Storage Accounts:
- Configure storage account settings
- Set up containers, queues, tables, and file shares
- Configure access policies and networking
- Enable encryption and security features

## When to use me

Use this skill when:
- Creating blob storage for applications
- Setting up Terraform state backend
- Creating file shares for VMs
- Implementing data lake storage

## Resource Templates

```hcl
# Basic Storage Account
resource "azurerm_storage_account" "main" {
  name                     = "${var.project_name}${var.environment}st"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"

  tags = local.common_tags
}

# Production-Ready Storage Account
resource "azurerm_storage_account" "production" {
  name                          = "${var.project_name}${var.environment}st"
  resource_group_name           = azurerm_resource_group.main.name
  location                      = azurerm_resource_group.main.location
  account_tier                  = "Standard"
  account_replication_type      = "GRS"
  min_tls_version               = "TLS1_2"
  enable_https_traffic_only     = true
  allow_nested_items_to_be_public = false

  blob_properties {
    versioning_enabled = true
    
    delete_retention_policy {
      days = 30
    }
    
    container_delete_retention_policy {
      days = 30
    }
  }

  network_rules {
    default_action = "Deny"
    bypass         = ["AzureServices"]
    ip_rules       = var.allowed_ips
  }

  tags = local.common_tags
}

# Blob Container
resource "azurerm_storage_container" "data" {
  name                  = "data"
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "private"
}
```

## Variables

```hcl
variable "storage_account_tier" {
  description = "Storage account tier (Standard or Premium)"
  type        = string
  default     = "Standard"
}

variable "storage_replication" {
  description = "Replication type (LRS, GRS, RAGRS, ZRS)"
  type        = string
  default     = "LRS"
}
```

## Outputs

```hcl
output "storage_account_name" {
  value = azurerm_storage_account.main.name
}

output "storage_account_primary_key" {
  value     = azurerm_storage_account.main.primary_access_key
  sensitive = true
}

output "storage_account_primary_connection_string" {
  value     = azurerm_storage_account.main.primary_connection_string
  sensitive = true
}
```

## Naming Rules

- 3-24 characters
- Lowercase letters and numbers only
- Must be globally unique

## Best Practices

1. Enable soft delete for blob and containers
2. Use HTTPS only
3. Set minimum TLS version to 1.2
4. Configure network rules for production
5. Enable versioning for critical data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
