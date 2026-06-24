---
name: terraform-module-best-practices
description: Usage examples and documentation Use when this capability is needed.
metadata:
  author: williamzujkowski
---

## Purpose & When-To-Use

Use when designing reusable Terraform modules that require:
- Input validation and type constraints
- Clear output contracts for module consumers
- Composition patterns for complex infrastructure
- Automated testing with Terratest
- Registry publishing and versioning

**Trigger conditions:**
- "Create a Terraform module for [infrastructure component]"
- "Add validation to Terraform variables"
- "Write Terratest tests for [module]"
- "Publish module to Terraform Registry"

## Pre-Checks

- **Authoritative time**: Set `NOW_ET` to ISO 8601 in `America/New_York` using NIST/time.gov semantics: `2025-10-26T02:31:29-0400`
- **Module type validation**: Ensure `module_type` is one of: network, compute, database, composite
- **Cloud provider support**: Verify provider-specific features and resource availability
- **Terraform version**: Check compatibility with `>= 1.0` for validation features
- **Source freshness**: Validate against current Terraform language spec and provider docs

## Procedure

### Tier 1 — Basic Module Structure (T1≤2000 tokens)

1. **Define module layout:**
   ```
   terraform-<name>/
     main.tf          # Primary resources
     variables.tf     # Input variables with validation
     outputs.tf       # Output values with descriptions
     versions.tf      # Provider version constraints
     README.md        # Usage documentation
     examples/        # Usage examples
       basic/
     tests/           # Terratest tests
   ```

2. **Create `variables.tf` with validation:**
   - Use `type` constraints (string, number, bool, list, map, object, set, tuple, any)
   - Add `validation` blocks for business logic constraints
   - Include `description` and `default` where appropriate
   - Use `sensitive = true` for secrets

3. **Define `outputs.tf` with clear contracts:**
   - Document output purpose and format
   - Use `description` for all outputs
   - Mark sensitive outputs with `sensitive = true`
   - Group related outputs logically

4. **Establish versioning in `versions.tf`:**
   - Pin Terraform version: `required_version = ">= 1.5"`
   - Pin provider versions with `~>` for minor updates
   - Document version rationale in comments

### Tier 2 — Module Composition & Publishing (T2≤6000 tokens)

1. **Implement composition patterns:**
   - **Child modules**: Extract repeated resources into sub-modules
   - **Dependency injection**: Pass outputs from one module as inputs to another
   - **Data-only modules**: Use `data` sources for discovery/lookups
   - **For-each patterns**: Use `for_each` with maps for dynamic resources

2. **Add comprehensive examples:**
   - **Basic example**: Minimal required inputs
   - **Complete example**: All features enabled
   - **Composition example**: Integration with other modules
   - Each example should be self-contained and runnable

3. **Prepare for Registry publishing:**
   - Follow naming convention: `terraform-<PROVIDER>-<NAME>`
   - Create Git tags matching semantic versioning: `v1.0.0`
   - Write comprehensive `README.md` with inputs/outputs tables
   - Add `LICENSE` file (Apache-2.0, MIT, etc.)
   - Include module registry metadata in repository

4. **Document module contracts:**
   - Input variable requirements and defaults
   - Output value structures and types
   - Provider configuration requirements
   - Resource naming conventions

### Tier 3 — Testing & CI/CD Integration (T3≤12000 tokens)

1. **Create Terratest tests:**
   - Install Terratest: `go get github.com/gruntwork-io/terratest/modules/terraform`
   - Write test structure:
     - Setup: Configure test inputs
     - Deploy: `terraform.InitAndApply(t, terraformOptions)`
     - Validate: Assert expected outputs and resource state
     - Teardown: `defer terraform.Destroy(t, terraformOptions)`
   - Test different scenarios: minimal, complete, error cases

2. **Implement validation tests:**
   - Test variable validation rules trigger correctly
   - Verify output schemas match documentation
   - Check resource dependencies and ordering
   - Validate provider authentication patterns

3. **Set up CI/CD pipeline:**
   - **Lint stage**: `terraform fmt -check`, `terraform validate`
   - **Security stage**: `tfsec`, `checkov`, `terrascan`
   - **Test stage**: Run Terratest suite in isolated environment
   - **Docs stage**: Auto-generate docs with `terraform-docs`
   - **Release stage**: Tag and publish to registry

4. **Add pre-commit hooks:**
   - Format code: `terraform fmt -recursive`
   - Validate syntax: `terraform validate`
   - Update docs: `terraform-docs markdown table --output-file README.md`
   - Security scan: `tfsec .`

## Decision Rules

- If `module_type == "composite"` → require Tier 2 composition patterns
- If `testing_required == true` → require Tier 3 Terratest implementation
- If publishing to registry → require semantic versioning and comprehensive README
- If variable validation fails in tests → emit specific validation error message
- If module has >5 required variables → recommend using `object` type for grouping
- If module creates >10 resources → recommend breaking into child modules
- **Abort conditions:**
  - Missing provider configuration
  - Circular module dependencies
  - Hardcoded secrets or credentials
  - Missing required variable descriptions

## Output Contract

**module_structure:**
```json
{
  "root": ["main.tf", "variables.tf", "outputs.tf", "versions.tf", "README.md"],
  "examples": ["basic", "complete"],
  "tests": ["<module_name>_test.go"],
  "docs": ["README.md", "CHANGELOG.md"]
}
```

**variables_tf:** String containing Terraform variable definitions with:
- `type` constraint
- `description` field
- `validation` blocks where applicable
- `default` values for optional inputs
- `sensitive` flag for secrets

**outputs_tf:** String containing output definitions with:
- `description` for each output
- `sensitive` flag where needed
- Logical grouping (e.g., network_ids, instance_details)

**test_code:** Go test file using Terratest framework with:
- Setup and teardown logic
- Terraform init/apply/destroy
- Output validation assertions
- Resource state checks

**examples:** Directory structure with runnable examples:
- `examples/basic/main.tf` — minimal configuration
- `examples/complete/main.tf` — all features enabled
- Each with `README.md` explaining usage

## Examples

**Example (≤30 lines): AWS VPC Module with Validation**
```hcl
# variables.tf
variable "vpc_cidr" {
  type        = string
  description = "CIDR block for VPC"
  validation {
    condition     = can(cidrhost(var.vpc_cidr, 0))
    error_message = "Must be valid IPv4 CIDR block."
  }
}

variable "subnet_count" {
  type        = number
  description = "Number of subnets to create"
  validation {
    condition     = var.subnet_count >= 2 && var.subnet_count <= 16
    error_message = "Subnet count must be between 2 and 16."
  }
}

# outputs.tf
output "vpc_id" {
  description = "ID of the created VPC"
  value       = aws_vpc.main.id
}

output "subnet_ids" {
  description = "List of subnet IDs"
  value       = aws_subnet.main[*].id
}
```

## Quality Gates

- **Token budgets**: T1≤2k (basic module structure), T2≤6k (composition + docs), T3≤12k (testing + CI/CD)
- **Example size**: ≤30 lines in `SKILL.md`; full examples in `examples/` directory
- **Variable validation**: All required variables must have `description`; validation rules for business logic
- **Output documentation**: All outputs must have `description` field
- **Testing coverage**: Minimum 3 test scenarios (basic, complete, error handling)
- **Security**: No hardcoded secrets; use `sensitive` flag; scan with `tfsec`
- **Versioning**: Semantic versioning for releases; provider version constraints in `versions.tf`
- **Formatting**: All code must pass `terraform fmt`
- **Documentation**: Auto-generated docs match actual inputs/outputs

## Resources

- **Terraform Module Development** (accessed 2025-10-26): https://developer.hashicorp.com/terraform/language/modules/develop
- **Terraform Module Publishing** (accessed 2025-10-26): https://developer.hashicorp.com/terraform/registry/modules/publish
- **Terraform Variable Validation** (accessed 2025-10-26): https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules
- **Terratest Documentation** (accessed 2025-10-26): https://terratest.gruntwork.io/docs/
- **Terratest Terraform Module** (accessed 2025-10-26): https://terratest.gruntwork.io/docs/getting-started/quick-start/#example-2-terraform
- **HashiCorp Module Standards** (accessed 2025-10-26): https://developer.hashicorp.com/terraform/language/modules/develop/structure
- **Terraform Testing Best Practices** (accessed 2025-10-26): https://developer.hashicorp.com/terraform/language/modules/testing-experiment

---
> Source: [williamzujkowski/cognitive-toolworks](https://github.com/williamzujkowski/cognitive-toolworks) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
