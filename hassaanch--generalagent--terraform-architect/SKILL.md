---
name: terraform-architect
description: | Use when this capability is needed.
metadata:
  author: hassaanch
---

# Terraform Architect

Generate production-ready Terraform modules and configurations for Azure infrastructure.

## What This Skill Does

- Generate Terraform configurations for Azure resources
- Design module structures for reusability
- Configure networking (VNets, Subnets, NSGs, Load Balancers)
- Set up AKS clusters with best practices
- Configure storage, Key Vault, and identity resources
- Provide state management guidance
- Generate variable files and outputs

## What This Skill Does NOT Do

- Execute Terraform commands on user's environment
- Access or modify cloud resources directly
- Handle AWS/GCP resources (Azure-focused)
- Manage Terraform state storage setup
- Provide cost estimation

---

## Before Implementation

Gather context for effective configuration:

| Source | Gather |
|--------|--------|
| **Requirements** | Resource types needed, environment (dev/prod) |
| **Existing Infra** | Existing resources to reference or import |
| **Naming Convention** | Company naming standards |
| **Networking** | IP ranges, connectivity requirements |

---

## Required Clarifications

Ask about USER'S context before generating:

1. **Resources**: "What Azure resources do you need?"
2. **Environment**: "Is this for dev, staging, or production?"
3. **Existing Infra**: "Are there existing resources to integrate with?"

## Optional Clarifications

Ask only if relevant to the user's context:

1. **Naming**: "What naming convention do you follow?" (if not evident from existing code)
2. **State**: "Where will Terraform state be stored?" (if not already configured)
3. **Modules**: "Do you want this as a reusable module or standalone configuration?"
4. **Tags**: "What tags should be applied to resources?"

---

## Official Documentation

| Resource | URL | Use For |
|----------|-----|---------|
| Terraform Azure Provider | https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs | Resource reference |
| Azure Naming Conventions | https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming | Naming best practices |
| Terraform Best Practices | https://developer.hashicorp.com/terraform/cloud-docs/recommended-practices | Structure and patterns |
| AKS Terraform Module | https://registry.terraform.io/modules/Azure/aks/azurerm/latest | Official AKS module |

---

## Workflow

### Phase 1: Requirements Analysis

```
1. Identify required Azure resources
2. Determine dependencies between resources
3. Identify existing resources to reference
4. Plan module structure
5. Define input variables needed
```

### Phase 2: Configuration Generation

```
1. Create provider configuration
2. Generate resource blocks with best practices
3. Define variables with descriptions and validation
4. Create outputs for resource attributes
5. Add data sources for existing resources
```

### Phase 3: Review & Optimization

```
1. Verify resource dependencies
2. Check for hardcoded values
3. Ensure proper tagging
4. Add lifecycle rules if needed
5. Include comments for complex logic
```

---

## Standard File Structure

```
terraform/
├── main.tf           # Main resource definitions
├── variables.tf      # Input variable declarations
├── outputs.tf        # Output definitions
├── providers.tf      # Provider configuration
├── versions.tf       # Required provider versions
├── locals.tf         # Local values
├── data.tf           # Data sources
├── terraform.tfvars  # Variable values (not in git)
└── modules/          # Reusable modules
    └── aks/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

## Provider Configuration

### Azure Provider

```hcl
terraform {
  required_version = ">= 1.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }

  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstate${var.environment}"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}

provider "azurerm" {
  features {}
}
```

---

## Common Resource Patterns

### Resource Group

```hcl
resource "azurerm_resource_group" "main" {
  name     = "rg-${var.project}-${var.environment}-${var.location_short}"
  location = var.location

  tags = local.common_tags
}
```

### Virtual Network with Subnets

```hcl
resource "azurerm_virtual_network" "main" {
  name                = "vnet-${var.project}-${var.environment}"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  address_space       = [var.vnet_address_space]

  tags = local.common_tags
}

resource "azurerm_subnet" "aks" {
  name                 = "snet-aks"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = [var.aks_subnet_prefix]
}
```

### AKS Cluster

**See `references/aks-patterns.md` for complete AKS configurations.**

### Load Balancer

**See `references/networking-patterns.md` for load balancer configurations.**

---

## Variable Best Practices

```hcl
variable "environment" {
  description = "Environment name (dev, staging, prod)"
  type        = string

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "location" {
  description = "Azure region for resources"
  type        = string
  default     = "eastus"
}

variable "tags" {
  description = "Additional tags for resources"
  type        = map(string)
  default     = {}
}
```

---

## Local Values Pattern

```hcl
locals {
  location_short = {
    "eastus"    = "eus"
    "westus"    = "wus"
    "westeurope" = "weu"
  }[var.location]

  common_tags = merge(
    {
      Environment = var.environment
      Project     = var.project
      ManagedBy   = "Terraform"
    },
    var.tags
  )
}
```

---

## Data Source Patterns

```hcl
# Reference existing resource group
data "azurerm_resource_group" "existing" {
  name = "existing-rg"
}

# Get current client configuration
data "azurerm_client_config" "current" {}

# Reference existing Key Vault
data "azurerm_key_vault" "existing" {
  name                = "existing-kv"
  resource_group_name = "existing-rg"
}
```

---

## Output Patterns

```hcl
output "resource_group_name" {
  description = "Name of the resource group"
  value       = azurerm_resource_group.main.name
}

output "aks_cluster_name" {
  description = "Name of the AKS cluster"
  value       = azurerm_kubernetes_cluster.main.name
}

output "aks_kube_config" {
  description = "Kubeconfig for AKS cluster"
  value       = azurerm_kubernetes_cluster.main.kube_config_raw
  sensitive   = true
}
```

---

## Lifecycle Rules

```hcl
resource "azurerm_resource_group" "main" {
  # ...

  lifecycle {
    prevent_destroy = true  # Prevent accidental deletion
  }
}

resource "azurerm_kubernetes_cluster" "main" {
  # ...

  lifecycle {
    ignore_changes = [
      default_node_pool[0].node_count,  # Ignore autoscaler changes
    ]
  }
}
```

---

## Common Mistakes to Avoid

1. **Hardcoded values** - Use variables
2. **Missing dependencies** - Use `depends_on` when implicit not enough
3. **No state locking** - Configure backend with locking
4. **Secrets in code** - Use Key Vault references
5. **No tagging** - Always include standard tags
6. **Wrong provider version** - Pin versions

---

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `Resource already exists` | Resource created outside Terraform | Use `terraform import` to bring into state |
| `Provider version mismatch` | Different versions across team | Pin version in `versions.tf` |
| `State lock timeout` | Previous run didn't release lock | Check for stuck processes, force unlock if safe |
| `Insufficient permissions` | Service principal lacks RBAC | Add required role assignments |
| `Quota exceeded` | Subscription limit reached | Request quota increase or use different region |
| `Circular dependency` | Resources reference each other | Refactor with `depends_on` or split resources |

---

## Output Checklist

Before delivering, verify:

- [ ] Provider version pinned
- [ ] All hardcoded values extracted to variables
- [ ] Variables have descriptions and validations
- [ ] Common tags applied to all resources
- [ ] Sensitive outputs marked as sensitive
- [ ] Dependencies properly defined
- [ ] Naming follows conventions
- [ ] Comments added for complex logic

---

## Reference Files

| File | When to Read |
|------|--------------|
| `references/aks-patterns.md` | For AKS cluster configurations |
| `references/networking-patterns.md` | For VNet, LB, and NSG patterns |
| `references/storage-patterns.md` | For storage account and blob patterns |
| `references/identity-patterns.md` | For managed identity and RBAC |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hassaanch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
