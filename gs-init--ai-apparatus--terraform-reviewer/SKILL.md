---
name: terraform-reviewer
description: Review Terraform changes for safety, security, and best practices. Use when the user asks to review Terraform code, check a terraform plan, audit infrastructure changes, or validate IaC. Use when this capability is needed.
metadata:
  author: gs-init
---

# Terraform Reviewer

Reviews Terraform changes for destructive operations, security misconfigurations, and compliance with internal infrastructure conventions. Designed to catch high-blast-radius mistakes before they reach production.

## Prerequisites

- Terraform files in the current repo or a path provided
- `terraform` CLI installed (for plan validation, optional)
- AWS credentials configured (for plan execution, optional)

## Scripts

**tf-review.sh** — Parses `terraform plan` output and flags destructive operations.

```bash
bash <skill-path>/scripts/tf-review.sh [plan-file]
```

## Workflow

1. **Gather Terraform changes**:

 If reviewing a PR or branch:
 ```bash
 git diff develop...HEAD -- '*.tf' '*.tfvars'
 git diff develop...HEAD --stat -- '*.tf' '*.tfvars'
 ```

 If reviewing the whole Terraform directory:
 ```bash
 find . -name '*.tf' -o -name '*.tfvars' | head -50
 ```

 Read all changed `.tf` and `.tfvars` files.

2. **Check for destructive operations** — These are the highest priority:

 | Operation | Risk Level | What to Check |
 |-----------|-----------|---------------|
 | `destroy` on any resource | CRITICAL | Any resource removal |
 | `replace` (force new) | CRITICAL | Name changes, AMI changes on EC2 |
 | Changing `engine_version` on RDS | HIGH | Causes downtime |
 | Modifying EKS cluster | HIGH | Node group changes, version upgrades |
 | Security group rule changes | HIGH | Could break connectivity |
 | IAM policy modifications | HIGH | Could break service permissions |
 | S3 bucket policy changes | MEDIUM | Could expose data |
 | CloudFront distribution changes | MEDIUM | Propagation delay |

3. **Run terraform plan** (if feasible):
 ```bash
 terraform init -backend=false
 terraform plan -out=tfplan 2>&1
 terraform show -json tfplan | jq '.resource_changes[] | select(.change.actions[] | contains("delete", "create"))'
 ```

 If plan isn't feasible (no backend access), do static analysis only.

4. **Security review checklist**:

 ### IAM
 - [ ] No `"Action": "*"` (wildcard actions)
 - [ ] No `"Resource": "*"` where it can be scoped
 - [ ] No inline policies on IAM users (use roles + groups)
 - [ ] Policies follow least privilege
 - [ ] No `iam:PassRole` with `"Resource": "*"`

 ### Networking
 - [ ] No security groups with `0.0.0.0/0` ingress on non-80/443 ports
 - [ ] No public subnets for database/internal services
 - [ ] VPC flow logs enabled
 - [ ] NACLs are not overly permissive

 ### Data
 - [ ] S3 buckets have encryption enabled (`server_side_encryption_configuration`)
 - [ ] S3 buckets have versioning enabled where appropriate
 - [ ] S3 buckets are not publicly accessible unless intentional
 - [ ] RDS instances have encryption at rest
 - [ ] RDS instances have automated backups enabled
 - [ ] Secrets are in AWS Secrets Manager or SSM Parameter Store, not in tfvars

 ### Compute
 - [ ] EKS node groups have appropriate instance types
 - [ ] Auto-scaling min/max values are reasonable
 - [ ] EC2 instances use IMDSv2

5. **Best practices review**:

 ### Structure
 - [ ] Variables have `description` and `type` defined
 - [ ] Outputs have `description` defined
 - [ ] Resources have meaningful names (not `resource1`, `test`)
 - [ ] Modules are versioned (source includes `?ref=`)
 - [ ] No hardcoded values — use variables or data sources

 ### Tagging
 All resources should have these tags at minimum:
 ```hcl
 tags = {
 Name = "<descriptive-name>"
 Environment = var.environment
 Service = "<service-name>"
 Team = "<team-name>"
 ManagedBy = "terraform"
 }
 ```

 ### State Safety
 - [ ] Critical resources have `lifecycle { prevent_destroy = true }`
 - [ ] State-modifying operations (import, mv, rm) are documented
 - [ ] Backend configuration uses encryption and locking (S3 + DynamoDB)

6. **Team-specific conventions**:
 - EKS namespaces should align with service names
 - ElastiCache clusters should use the team's standard node types
 - Kinesis streams should have appropriate shard counts for the environment
 - CloudFront distributions should use the standard WAF ACL
 - All resources should be in the correct AWS account/region

7. **Generate the review report**:

 ```markdown
 ## Terraform Review

 **Files Changed**: <count>
 **Resources Affected**: <count> added, <count> changed, <count> destroyed

 ### CRITICAL — Destructive Changes
 - <resource> will be DESTROYED — <reason and impact>

 ### HIGH — Security Concerns
 - <finding>

 ### MEDIUM — Best Practice Violations
 - <finding>

 ### LOW — Suggestions
 - <finding>

 ### Approval Recommendation
 <APPROVE / REQUEST CHANGES / NEEDS DISCUSSION>
 <reasoning>
 ```

8. **Present findings** and ask the user if they want to:
 - Apply suggested fixes
 - Add lifecycle protection
 - Modify security configurations

## Rules

- NEVER run `terraform apply` — only `plan` and read-only operations
- Destructive changes to databases, EKS clusters, and S3 buckets are always CRITICAL
- If `prevent_destroy` is being removed, flag as CRITICAL and ask why
- Always consider the environment (dev changes are lower risk than production)
- If unsure about impact, recommend running plan in a non-production environment first
- Flag any resource that would cause downtime during replacement
- Check that state operations (import, mv) are documented in the PR

---
> Source: [gs-init/ai-apparatus](https://github.com/gs-init/ai-apparatus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
