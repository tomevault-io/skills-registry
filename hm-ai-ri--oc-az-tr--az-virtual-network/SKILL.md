---
name: az-virtual-network
description: Create and configure Azure Virtual Networks with Terraform Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I do

Help create and manage Azure Virtual Networks:
- Configure VNets with address spaces
- Create subnets with proper CIDR blocks
- Set up Network Security Groups (NSGs)
- Configure service endpoints and delegations

## When to use me

Use this skill when:
- Setting up network infrastructure for VMs
- Creating isolated network environments
- Implementing hub-spoke network topology
- Configuring private endpoints

## Resource Templates

```hcl
# Virtual Network
resource "azurerm_virtual_network" "main" {
  name                = "${local.name_prefix}-vnet"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  address_space       = ["10.0.0.0/16"]

  tags = local.common_tags
}

# Subnets
resource "azurerm_subnet" "web" {
  name                 = "web-subnet"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.1.0/24"]
}

resource "azurerm_subnet" "app" {
  name                 = "app-subnet"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.2.0/24"]
}

resource "azurerm_subnet" "db" {
  name                 = "db-subnet"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.0.3.0/24"]

  service_endpoints = ["Microsoft.Sql", "Microsoft.Storage"]
}

# Network Security Group
resource "azurerm_network_security_group" "web" {
  name                = "${local.name_prefix}-web-nsg"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name

  security_rule {
    name                       = "AllowHTTPS"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "443"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  security_rule {
    name                       = "AllowHTTP"
    priority                   = 110
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "80"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }

  tags = local.common_tags
}

# Associate NSG with Subnet
resource "azurerm_subnet_network_security_group_association" "web" {
  subnet_id                 = azurerm_subnet.web.id
  network_security_group_id = azurerm_network_security_group.web.id
}
```

## Variables

```hcl
variable "vnet_address_space" {
  description = "Address space for the virtual network"
  type        = list(string)
  default     = ["10.0.0.0/16"]
}

variable "subnets" {
  description = "Map of subnet configurations"
  type = map(object({
    address_prefix    = string
    service_endpoints = list(string)
  }))
  default = {
    web = {
      address_prefix    = "10.0.1.0/24"
      service_endpoints = []
    }
    app = {
      address_prefix    = "10.0.2.0/24"
      service_endpoints = []
    }
  }
}
```

## Outputs

```hcl
output "vnet_id" {
  value = azurerm_virtual_network.main.id
}

output "vnet_name" {
  value = azurerm_virtual_network.main.name
}

output "subnet_ids" {
  value = {
    web = azurerm_subnet.web.id
    app = azurerm_subnet.app.id
    db  = azurerm_subnet.db.id
  }
}
```

## Common Subnet Sizes

| CIDR | Hosts | Use Case |
|------|-------|----------|
| /24  | 251   | Standard subnet |
| /25  | 123   | Medium workloads |
| /26  | 59    | Small workloads |
| /27  | 27    | Minimal workloads |
| /28  | 11    | Gateway subnet |

## Best Practices

1. Plan IP address space carefully before deployment
2. Use separate subnets for different tiers (web, app, db)
3. Apply NSGs at subnet level
4. Reserve address space for future growth
5. Use service endpoints for Azure PaaS services

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
