---
name: azure-architecture-review
description: Review Terraform Azure code against Microsoft Cloud Adoption Framework (CAF) and Azure Well-Architected Framework (WAF). Use this skill when reviewing Terraform configurations, validating code against Microsoft frameworks, checking infrastructure-as-code compliance, or performing architecture reviews of .tf files before deployment. Use when this capability is needed.
metadata:
  author: thomast1906
---

# Azure Architecture Review Skill

This skill **reviews Terraform code** for Azure resources against Microsoft's Cloud Adoption Framework (CAF) and Well-Architected Framework (WAF). It analyzes `.tf` files to ensure your infrastructure-as-code follows Microsoft's frameworks.

## When to Use This Skill

- **Reviewing Terraform code** - Check `.tf` files against CAF/WAF before deployment
- **Pull request reviews** - Validate infrastructure-as-code changes
- **Architecture validation** - Ensure Terraform designs follow Microsoft frameworks
- **Before terraform apply** - Catch compliance issues early in development
- **Code refactoring** - Align existing Terraform with CAF/WAF best practices
- **Compliance audits** - Review infrastructure-as-code for governance

## Applicable to ALL Azure Resources

This skill works for:
- **Networking**: Virtual Networks, Firewalls, VPN Gateways, Load Balancers
- **Compute**: Virtual Machines, App Service, AKS, Container Instances
- **Storage**: Storage Accounts, Managed Disks, File Shares
- **Databases**: SQL Database, Cosmos DB, PostgreSQL, MySQL
- **Security**: Key Vault, Managed Identity, Private Endpoints
- **Monitoring**: Log Analytics, Application Insights, Alerts
- **And more**: Any Azure service with Terraform support

## Prerequisites

**Required MCP Tools**:
- `mcp_azure_mcp_documentation` - Search Microsoft Learn documentation
- `mcp_azure_mcp_azureterraformbestpractices` - Get Terraform-specific Azure guidance

**Context Needed**:
- Understanding of the infrastructure being reviewed
- Business requirements (SLA, security, compliance)
- Current state (greenfield vs brownfield)

## Review Methodology

### Step 1: Get Terraform Best Practices (MANDATORY)

**Always call this FIRST before generating or reviewing Terraform code:**

```bash
# MCP Command
mcp_azure_mcp_azureterraformbestpractices
  command: get
  intent: "Get Azure Terraform best practices before code review"
```

**Returns**: Terraform validation workflow (validate → plan → apply), Azure-specific patterns, security defaults, naming conventions

### Step 2: Search Cloud Adoption Framework Documentation

**Query Pattern**: `"Cloud Adoption Framework {resource-type} {design-area}"`

**Examples**:
```bash
# Networking
query: "Cloud Adoption Framework hub spoke network topology"

# Storage
query: "Cloud Adoption Framework storage account security encryption"

# Compute
query: "Cloud Adoption Framework App Service landing zone networking"

# Databases
query: "Cloud Adoption Framework Azure SQL Database security"

# Security
query: "Cloud Adoption Framework Key Vault secrets management"
```

**Key CAF Design Areas** (apply to ALL resources):
1. **Resource Organization** - Naming, resource groups, subscriptions
2. **Security** - Authentication, encryption, network isolation, RBAC
3. **Network Topology** - VNet integration, private endpoints, connectivity
4. **Identity & Access** - Managed identities, RBAC, Azure AD
5. **Governance** - Tags, Azure Policy, diagnostics, cost tracking

### Step 3: Search Well-Architected Framework Documentation

**Query Pattern**: `"Well-Architected Framework {pillar} {service-name} {concern}"`

**Generic Pillar Queries**:
```bash
# Reliability
query: "Well-Architected Framework reliability availability zones disaster recovery"

# Security  
query: "Well-Architected Framework security encryption network access control"

# Cost Optimization
query: "Well-Architected Framework cost optimization pricing SKU right-sizing"

# Operational Excellence
query: "Well-Architected Framework operational excellence monitoring diagnostics"

# Performance Efficiency
query: "Well-Architected Framework performance efficiency scalability throughput"
```

**Service-Specific Pattern**: `"{Service} Well-Architected {pillar}"`
```bash
# Examples
query: "Storage Account Well-Architected security encryption"
query: "App Service Well-Architected reliability availability zones"
query: "Azure SQL Database Well-Architected reliability high availability"
query: "Key Vault Well-Architected security access policies"
```

### Step 4: Service-Specific Guidance

Search for service-specific WAF guidance:

```bash
# Pattern: "{Azure Service} Well-Architected best practices"
query: "Azure Firewall Well-Architected best practices"
query: "VPN Gateway Well-Architected reliability"
query: "Virtual Machines Well-Architected best practices"
query: "Azure Kubernetes Service Well-Architected security"
```

## Validation Framework

### Universal Checklist (ALL Resources)

**CAF Validation**:
- [ ] Naming convention: `{type}-{workload}-{env}-{region}-{instance}`
- [ ] Required tags: environment, project, owner, cost-center, managed-by
- [ ] Resource organization: appropriate subscription/resource group
- [ ] Documentation: README with usage examples

**WAF Reliability**:
- [ ] Zone redundancy or appropriate SLA
- [ ] Backup/recovery strategy
- [ ] Health monitoring configured

**WAF Security**:
- [ ] Managed identity (not service principal)
- [ ] Private endpoint (no public access)
- [ ] Encryption at rest and in transit
- [ ] RBAC with least privilege
- [ ] No hardcoded secrets

**WAF Cost**:
- [ ] Right-sized SKU for workload
- [ ] Appropriate replication/redundancy level
- [ ] Cost tags for tracking

**WAF Operations**:
- [ ] Diagnostic settings enabled
- [ ] Log Analytics integration
- [ ] Infrastructure as Code (Terraform)
- [ ] Input validation

**WAF Performance**:
- [ ] SKU meets performance requirements
- [ ] Scaling configured appropriately
- [ ] Optimised for workload patterns

> **Note**: Detailed resource-specific validation checklists are in [references/REFERENCE.md](references/REFERENCE.md)

## Review Output Template

### Summary
- **Architecture Type**: [Hub-Spoke / Virtual WAN / Other]
- **Environment**: [Production / Non-Production]
- **Overall Compliance Score**: [X/100]

### Framework Alignment

**CAF Score**: [X%] ✅ / ⚠️ / ❌
- Network Topology: [findings]
- Naming Conventions: [findings]
- Resource Organization: [findings]
- Tagging Strategy: [findings]

### WAF Pillar Scores

| Pillar | Score | Status | Key Findings |
|--------|-------|--------|--------------|
| Reliability | X% | ✅/⚠️/❌ | [summary] |
| Security | X% | ✅/⚠️/❌ | [summary] |
| Cost Optimization | X% | ✅/⚠️/❌ | [summary] |
| Operational Excellence | X% | ✅/⚠️/❌ | [summary] |
| Performance Efficiency | X% | ✅/⚠️/❌ | [summary] |

### Recommended Actions

#### High Priority
1. **[Issue]** - [Recommendation with code example]
   ```hcl
   # Current (❌)
   [bad code]
   
   # Recommended (✅)
   [good code]
   ```
   Reference: [Link to Microsoft docs]

#### Medium Priority
1. **[Enhancement]** - [Recommendation]

#### Low Priority
1. **[Optional Enhancement]** - [Recommendation]

## Example: Storage Account Review

```markdown
## Review Request
Review my storage account Terraform for CAF/WAF compliance.

## Steps Taken
1. ✅ Called `azureterraformbestpractices get`
2. ✅ Searched CAF: "Cloud Adoption Framework storage account security"
3. ✅ Searched WAF: "Storage Account Well-Architected security encryption"

## Overall Score: 75/100

### Framework Scores
- **CAF**: 85% ✅ (naming correct, tags present)
- **WAF Reliability**: 60% ⚠️ (LRS - not zone-redundant)
- **WAF Security**: 70% ⚠️ (public access enabled)
- **WAF Cost**: 90% ✅
- **WAF Operations**: 80% ✅

### Critical Findings

#### High Priority

**1. Public Access Enabled** (Security)
```hcl
# Current (❌)
public_network_access_enabled = true

# Recommended (✅)
public_network_access_enabled = false

resource "azurerm_private_endpoint" "storage" {
  name                = "pe-${var.storage_account_name}"
  location            = var.location
  resource_group_name = var.resource_group_name
  subnet_id           = var.subnet_id
  
  private_service_connection {
    name                           = "psc-storage"
    private_connection_resource_id = azurerm_storage_account.this.id
    subresource_names             = ["blob"]
    is_manual_connection          = false
  }
}
```
Reference: [Storage Account Private Endpoints](https://learn.microsoft.com/azure/storage/common/storage-private-endpoints)

**2. LRS Not Zone-Redundant** (Reliability)
```hcl
# Current (⚠️)
account_replication_type = "LRS"  # Single zone

# Production (✅)
account_replication_type = "ZRS"   # Zone-redundant
# OR
account_replication_type = "GZRS"  # Geo-zone-redundant
```
Reference: [Storage Redundancy](https://learn.microsoft.com/azure/storage/common/storage-redundancy)
```

## Example: App Service Review

```markdown
## Review Request
Check App Service Terraform against Microsoft best practices.

## Overall Score: 82/100

### High Priority

**Zone Redundancy Not Enabled** (Reliability)
```hcl
# Current (⚠️)
sku_name = "P1v2"  # Doesn't support zones

# Production (✅)
sku_name               = "P1v3"  # PremiumV3
zone_balancing_enabled = true    # Zone redundancy
```
Impact: Improves availability from 99.95% → 99.99%

Reference: [App Service Zone Redundancy](https://learn.microsoft.com/azure/app-service/how-to-zone-redundancy)

### Medium Priority

**Health Check Not Configured** (Reliability)
```hcl
# Add this (✅)
site_config {
  health_check_path                 = "/health"
  health_check_eviction_time_in_min = 5
}
```
```

## Tips and Best Practices

### 🔍 Search Query Optimization

**Good queries** (specific, actionable):
- "Cloud Adoption Framework {service} security best practices"
- "Well-Architected Framework {service} reliability zones"
- "{service} encryption private endpoint security baseline"

**Poor queries** (too broad):
- "Azure best practices" ❌
- "Cloud Adoption Framework" ❌
- "security" ❌

### 🎯 Resource-Agnostic Approach

The methodology is the same for ALL resources:
1. Call Terraform best practices (always first)
2. Search CAF for resource-specific guidance
3. Search WAF for each pillar
4. Generate report with scores and recommendations

**The queries change, the methodology doesn't!**

### ⚡ Common Gaps to Check

1. **Missing diagnostic settings** - Most common WAF gap
2. **No NSG flow logs** - Security and operational visibility
3. **Hardcoded values** - Should use variables
4. **No cost estimates** - Users need to understand spend
5. **Missing examples** - Modules need working examples

### 🎯 Validation Priority

1. **Security** - Always highest priority (breaches are expensive)
2. **Reliability** - Downtime impacts business
3. **Operational Excellence** - Reduces long-term costs
4. **Cost Optimization** - Balance with other pillars
5. **Performance Efficiency** - Optimize after stability

## Scoring Framework

**Overall Score Calculation**:
```
Total Score = (CAF Score × 0.4) + (WAF Score × 0.6)
CAF Score = (Topology + Naming + Tagging + Organization) / 4
WAF Score = (Reliability + Security + Cost + OpEx + Performance) / 5
```

**Grade Interpretation**:
- **90-100%**: ✅ Excellent - Production ready
- **80-89%**: ✅ Good - Minor enhancements needed
- **70-79%**: ⚠️ Acceptable - Several improvements required
- **60-69%**: ⚠️ Needs Work - Significant gaps
- **Below 60%**: ❌ Not Recommended - Major revisions needed

## Additional Resources

- **Detailed Validation Checklists**: See [references/REFERENCE.md](references/REFERENCE.md) for resource-specific validation criteria
- **MCP Commands**: Full command syntax in [references/REFERENCE.md](references/REFERENCE.md)
- **Scoring Details**: Detailed pillar scoring in [references/REFERENCE.md](references/REFERENCE.md)
- **Common Findings**: Examples of fixes in [references/REFERENCE.md](references/REFERENCE.md)

## Integration with Other Skills

- **azure-verified-modules** - Learn implementation patterns from AVM
- **terraform-security-scan** - Deep security validation with tfsec/checkov
- **github-actions-terraform** - CI/CD pipeline best practices

## References

- [Azure Cloud Adoption Framework](https://learn.microsoft.com/azure/cloud-adoption-framework/)
- [Azure Well-Architected Framework](https://learn.microsoft.com/azure/well-architected/)
- [Hub-Spoke Network Topology](https://learn.microsoft.com/azure/architecture/networking/architecture/hub-spoke)
- [Azure Landing Zones](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/)
- [Network Design Area](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomast1906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
