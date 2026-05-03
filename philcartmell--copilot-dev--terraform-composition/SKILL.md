---
name: terraform-composition
description: This skill defines conventions and best practices for composing root Terraform modules that orchestrate centralized reusable modules. Use when this capability is needed.
metadata:
  author: philcartmell
---

# Terraform Composition Best Practices

This skill defines conventions and best practices for composing root Terraform modules that orchestrate centralized reusable modules.

## Module Usage Guidelines

### When a Module Exists
- **Always reference the existing module** from the centralized repository
- Use the module source format: `github.com/Philcartmell/terraform-modules//module-name`
- Check the module's variables and outputs to ensure proper usage
- Don't duplicate functionality that already exists in a module

Example:
```hcl
module "storage" {
  source = "github.com/Philcartmell/terraform-modules//azure-storage-account"
  
  name                     = "mystorageaccount"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "GRS"
  
  tags = local.common_tags
}
```

### When a Module Doesn't Exist
- **Create a local module for prototyping** in a `modules/` directory at the workspace root
- Structure the local module with best practices
- Document clearly that this is a prototype module
- Add a note that it can be promoted to the centralized repository later
- Use relative path references: `source = "./modules/module-name"`

Example local module structure:
```
modules/
  └── new-azure-service/
      ├── README.md
      ├── main.tf
      ├── variables.tf
      ├── outputs.tf
      └── versions.tf
```

## Root Module Conventions

Our root Terraform modules follow a **map-based configuration approach** for flexibility and scalability.

### Variable Structure

1. **Map-Based Variables**
   - All resources are defined using map variables (not individual variables)
   - Each resource type has its own map variable:
     - `resource_groups` - Map of resource groups to create
     - `keyvaults` - Map of Key Vaults to create
     - `storage_accounts` - Map of Storage Accounts to create
   - Map keys become the resource identifiers in Terraform state
   - Map keys are used in resource naming (e.g., `phil-{key}-{random}`)

2. **Resource Group References**
   - Each resource object includes a `resource_group_key` field
   - This key references an entry in the `resource_groups` map
   - Enables flexible resource placement across multiple resource groups

3. **Optional Overrides**
   - Each resource supports optional `location` override (inherits from resource group if not specified)
   - Each resource supports optional `tags` that merge with common tags
   - Use `optional()` type constraint for fields with defaults

Example variable structure:
```hcl
variable "keyvaults" {
  description = "Map of Key Vaults to create"
  type = map(object({
    resource_group_key           = string
    location                     = optional(string)
    sku_name                     = optional(string, "standard")
    enabled_for_disk_encryption  = optional(bool, true)
    purge_protection_enabled     = optional(bool, false)
    tags                         = optional(map(string), {})
  }))
  default = {}
}
```

### Resource Implementation

1. **For_Each Pattern**
   - Use `for_each` to iterate over map variables
   - Reference map key with `each.key`
   - Reference map value with `each.value`
   - Never use `count` for multiple resources

2. **Resource Naming**
   - Generate unique names using map keys and random suffixes
   - Key Vaults: `phil-{each.key}-{random_string.suffix[each.key].result}`
   - Storage Accounts: `phil{each.key}{random_string.storage_suffix[each.key].result}` (no hyphens)
   - Create separate `random_string` resources for each resource type using `for_each`

3. **Location Inheritance**
   - Use `coalesce()` to provide fallback values
   - Pattern: `coalesce(each.value.location, azurerm_resource_group.main[each.value.resource_group_key].location)`
   - Allows per-resource location override while defaulting to resource group location

4. **Tag Merging**
   - Always merge common tags with resource-specific tags
   - Pattern: `tags = merge(local.common_tags, each.value.tags)`

Example resource implementation:
```hcl
resource "random_string" "suffix" {
  for_each = var.keyvaults
  length   = 6
  special  = false
  upper    = false
}

module "keyvault" {
  for_each = var.keyvaults
  source   = "github.com/Philcartmell/terraform-modules//azure-keyvault"
  
  name                = "phil-${each.key}-${random_string.suffix[each.key].result}"
  resource_group_name = azurerm_resource_group.main[each.value.resource_group_key].name
  location            = coalesce(each.value.location, azurerm_resource_group.main[each.value.resource_group_key].location)
  tenant_id           = data.azurerm_client_config.current.tenant_id
  sku_name            = each.value.sku_name
  
  tags = merge(local.common_tags, each.value.tags)
}
```

### Output Structure

1. **Map-Based Outputs**
   - All outputs return maps keyed by the resource key
   - Use `for` expressions to transform module outputs into maps
   - Group related outputs (separate sensitive from non-sensitive)

2. **Output Patterns**
   ```hcl
   output "keyvaults" {
     description = "Map of Key Vault details"
     value = {
       for k, kv in module.keyvault : k => {
         id        = kv.id
         name      = kv.name
         vault_uri = kv.vault_uri
       }
     }
   }
   
   output "keyvault_secrets" {
     description = "Map of sensitive Key Vault details"
     value = {
       for k, kv in module.keyvault : k => {
         primary_key = kv.primary_key
       }
     }
     sensitive = true
   }
   ```

### File Organization

1. **Root Module Structure**
   - `main.tf` - Primary resource definitions (resource groups, core infrastructure)
   - `variables.tf` - Input variable declarations (map-based)
   - `outputs.tf` - Output value declarations (map-based)
   - `versions.tf` - Provider version constraints
   - `locals.tf` - Local values (common tags, etc.)
   - `data.tf` - Data source declarations (when significant)
   - **Resource-specific files** - Named after the resource type they manage:
     - `keyvault.tf` - Key Vault resources and module calls
     - `storage.tf` - Storage Account resources and module calls
     - `network.tf` - Virtual Network resources and module calls
     - `compute.tf` - Virtual Machine resources and module calls
     - `database.tf` - Database resources and module calls
   
   **Benefits of resource-specific files:**
   - Easier to locate and maintain specific resource types
   - Clear separation of concerns
   - Simplifies code reviews and collaboration
   - Each file calls either the remote module (if it exists) or local module (if prototyping)

### Environment Configuration

1. **Environment-Specific Variables**
   - Create an `environments/` folder in the workspace root
   - Store environment-specific `.tfvars` files in this folder:
     - `environments/sandbox.tfvars` - Sandbox/development environment
     - `environments/dev.tfvars` - Development environment
     - `environments/staging.tfvars` - Staging environment
     - `environments/prod.tfvars` - Production environment
   
2. **Running Terraform with Environment Files**
   ```bash
   # Sandbox environment
   terraform plan -var-file="environments/sandbox.tfvars"
   terraform apply -var-file="environments/sandbox.tfvars"
   
   # Production environment
   terraform plan -var-file="environments/prod.tfvars"
   terraform apply -var-file="environments/prod.tfvars"
   ```

3. **TFVars File Structure**
   - Define all resources in hierarchical map structure
   - Include commented examples for adding multiple instances
   - Show clear relationship between resources via `resource_group_key`
   - Keep sensitive values out of tfvars files (use Azure Key Vault references)
   - Document required variables in `terraform.tfvars.example`
   - Use consistent naming across environments

Example `environments/sandbox.tfvars`:
```hcl
resource_groups = {
  "phil-rg" = {
    location = "East US"
    tags = { Purpose = "Sandbox" }
  }
}

keyvaults = {
  "kv" = {
    resource_group_key = "phil-rg"
    sku_name          = "standard"
    tags              = { Service = "KeyVault" }
  }
  "kv-logs" = {
    resource_group_key = "phil-rg"
    sku_name          = "premium"
    tags              = { Service = "KeyVault", Purpose = "Logging" }
  }
}

storage_accounts = {
  "sa" = {
    resource_group_key       = "phil-rg"
    account_tier             = "Standard"
    account_replication_type = "LRS"
    tags                     = { Service = "Storage" }
  }
}
```

### Variable Best Practices

1. **Type Definitions**
   - Always provide descriptions for variables
   - Set appropriate types using map(object({...}))
   - Use validation blocks for constrained values
   - Provide sensible defaults where appropriate
   - Mark sensitive variables with `sensitive = true`
   - Use `optional()` for fields with defaults

Example with validation:
```hcl
variable "keyvaults" {
  description = "Map of Key Vaults to create"
  type = map(object({
    resource_group_key           = string
    location                     = optional(string)
    sku_name                     = optional(string, "standard")
    enabled_for_disk_encryption  = optional(bool, true)
    purge_protection_enabled     = optional(bool, false)
    tags                         = optional(map(string), {})
  }))
  default = {}

  validation {
    condition = alltrue([
      for k, v in var.keyvaults : contains(["standard", "premium"], v.sku_name)
    ])
    error_message = "Key Vault SKU must be either 'standard' or 'premium'."
  }
}
```

2. **Naming Conventions**
   - Use lowercase with underscores for all identifiers
   - Resource names should be descriptive: `azurerm_resource_group.main` not `azurerm_resource_group.rg`
   - Variable names should be clear and self-documenting
   - Use plural names for map variables: `keyvaults`, `storage_accounts`, `resource_groups`

### Benefits of This Approach

- **Scalability**: Easy to add/remove resources by modifying maps
- **Flexibility**: Each resource can have unique configuration
- **DRY**: No code duplication when creating multiple similar resources
- **Clear References**: Explicit relationships through `resource_group_key`
- **Type Safety**: Strong typing with validation at the variable level
- **Clean State**: Resources keyed logically in Terraform state

## Terraform Best Practices

### Code Quality

1. **Formatting**
   - Run `terraform fmt -recursive` to format code consistently
   - Use 2-space indentation
   - Align equals signs in resource blocks for readability

2. **Initialization and Validation (MANDATORY)**
   - **ALWAYS run these commands in order for EVERY Terraform task:**
     1. `terraform init` - Initialize the workspace and download providers/modules
     2. `terraform fmt -recursive` - Format all Terraform files consistently
     3. `terraform validate` - Check syntax and verify configuration
     4. `terraform plan -var-file="environments/<env>.tfvars"` - Preview changes before applying
   - This sequence is **NON-NEGOTIABLE** and must be executed before any plan or apply operation
   - Implement variable validation where appropriate
   - Fix all errors before proceeding to deployment

3. **Comments and Documentation**
   - Comment complex logic or non-obvious configurations
   - Keep comments up-to-date with code changes
   - Use comments to explain "why" not "what"

### Security Best Practices

1. **Secrets Management**
   - Never hardcode secrets in Terraform files
   - Use Azure Key Vault for secret storage
   - Use `sensitive = true` for sensitive variables and outputs
   - Reference secrets from Key Vault using data sources

2. **State Management**
   - Always use remote state (Azure Storage with encryption)
   - Enable state locking to prevent concurrent modifications
   - Never commit state files to version control

3. **Access Control**
   - Use managed identities where possible
   - Follow principle of least privilege
   - Enable audit logging on resources

### Azure-Specific Best Practices

1. **Resource Naming**
   - Follow Azure naming conventions and restrictions
   - Use name prefixes/suffixes to indicate environment and purpose
   - Keep names within Azure length limits

2. **Resource Groups**
   - Group related resources logically
   - Use consistent naming for resource groups
   - Consider lifecycle and management boundaries

3. **Regions and Availability**
   - Specify explicit locations rather than using defaults
   - Consider paired regions for disaster recovery
   - Be aware of service availability in different regions

4. **Cost Management**
   - Use appropriate SKUs for environments (Basic for dev, Standard/Premium for prod)
   - Implement auto-shutdown for dev/test resources
   - Tag resources for cost allocation and tracking

### Module Development (for centralized modules)

1. **Module Structure**
   - Keep modules focused and single-purpose
   - Make modules reusable and configurable
   - Provide comprehensive README.md with examples
   - Version your modules using Git tags

2. **Module Documentation**
   - Document all variables with descriptions
   - Provide usage examples in README.md
   - Document any prerequisites or dependencies
   - Include expected outputs

3. **Module Testing**
   - Consider adding example configurations
   - Test modules in isolated environments before production use

### General Guidelines

- **DRY Principle**: Don't Repeat Yourself - use modules and locals
- **Immutable Infrastructure**: Prefer replacing resources over modifying them
- **Plan Before Apply**: Always review terraform plan output
- **Incremental Changes**: Make small, focused changes rather than large sweeping changes
- **Documentation**: Keep README files updated with current usage

## Workflow for Root Module Composition

1. **Create resource-specific file**
   - Name the file after the resource type (e.g., `keyvault.tf`, `storage.tf`)
   - Call the remote module from centralized repository
   - Use `for_each` with map variable
   
   Example `keyvault.tf`:
   ```hcl
   resource "random_string" "suffix" {
     for_each = var.keyvaults
     length   = 6
     special  = false
     upper    = false
   }

   module "keyvault" {
     for_each = var.keyvaults
     source   = "github.com/Philcartmell/terraform-modules//azure-keyvault"
     
     name                = "phil-${each.key}-${random_string.suffix[each.key].result}"
     resource_group_name = azurerm_resource_group.main[each.value.resource_group_key].name
     location            = coalesce(each.value.location, azurerm_resource_group.main[each.value.resource_group_key].location)
     tenant_id           = data.azurerm_client_config.current.tenant_id
     sku_name            = each.value.sku_name
     
     tags = merge(local.common_tags, each.value.tags)
   }
   ```

2. **Define map-based variables**
   - Create map variable with object type
   - Include `resource_group_key` field
   - Add optional overrides for location and tags
   - Include validation blocks

3. **Set up environment configuration**
   - Create `environments/` folder if it doesn't exist
   - Create environment-specific `.tfvars` files (starting with `sandbox.tfvars`)
   - Document variables in `terraform.tfvars.example`

4. **Create map-based outputs**
   - Transform module outputs into maps using `for` expressions
   - Separate sensitive from non-sensitive outputs
   - Use descriptive output names

5. **ALWAYS initialize, format, and validate the configuration:**
   - **MANDATORY**: Run `terraform init` to download providers and modules
   - **MANDATORY**: Run `terraform fmt -recursive` to format all files consistently
   - **MANDATORY**: Run `terraform validate` to check syntax and configuration validity
   - Fix any issues before proceeding to plan/apply
   - These steps are required for EVERY Terraform composition task

6. **Test with environment file**
   - Test thoroughly using `terraform plan -var-file="environments/sandbox.tfvars"`
   - Verify changes before applying with `terraform apply -var-file="environments/sandbox.tfvars"`

7. **Document the configuration**
   - Update README with resource-specific details
   - Document environment-specific variables
   - Include usage examples with environment files
   - Show how to add multiple instances of resources

## When Composing Root Modules

- **Use resource-specific file naming:**
  - Create `keyvault.tf` for Key Vault resources
  - Create `storage.tf` for Storage Account resources
  - Create `network.tf` for networking resources
  - Keep `main.tf` for resource groups and core infrastructure

- **Use environment-based configuration:**
  - Create `environments/` folder structure
  - Generate `environments/sandbox.tfvars` for initial testing
  - Include examples of using `-var-file` in documentation

- **Implement map-based patterns:**
  - All resources defined as maps in variables
  - Use `for_each` to iterate over maps
  - Include `resource_group_key` references
  - Support optional location and tags overrides
  - Use `coalesce()` for location inheritance
  - Use `merge()` for tag merging

- **Create complete, production-ready code:**
  - Include proper error handling and validation
  - Add comprehensive variable descriptions
  - Generate map-based outputs
  - Follow Azure naming conventions

- **ALWAYS ensure quality and working terraform code:**
  - **MANDATORY**: Run `terraform init` before any other Terraform commands
  - **MANDATORY**: Run `terraform fmt -recursive` to ensure consistent formatting
  - **MANDATORY**: Run `terraform validate` to verify the configuration is syntactically valid
  - Address any errors or warnings before considering the task complete
  - These commands ensure high-quality, production-ready code and MUST be executed for every Terraform task
  - Only after successful init, fmt, and validate should you proceed to plan/apply

- **Version Control Best Practices:**
  - Commit `.terraform.lock.hcl` to ensure consistent provider versions
  - Commit all `.tfvars` files in `environments/` folder (unless they contain secrets)
  - Add `terraform.tfvars` to `.gitignore` (use environments folder instead)
  - Add `*.tfstate` and `*.tfstate.backup` to `.gitignore`
  - Add `.terraform/` directory to `.gitignore`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/philcartmell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
