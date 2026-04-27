---
name: terraform-state-manager
description: Manages Terraform state operations including importing existing resources, moving resources between states, removing resources from state, and migrating state backends. This skill should be used when users need to import infrastructure into Terraform, refactor resource addresses, fix state issues, or migrate state storage locations.
metadata:
  author: armanzeroeight
---

# Terraform State Manager

This skill provides safe workflows for Terraform state operations and troubleshooting.

## When to Use

Use this skill when:
- Importing existing infrastructure into Terraform
- Moving resources to different addresses (renaming)
- Removing resources from state without destroying them
- Migrating state between backends (local to S3, etc.)
- Recovering from state corruption or conflicts
- Splitting or merging state files

## Critical Safety Rules

**ALWAYS follow these rules when working with state:**

1. **Backup first**: Create state backup before any operation
2. **Plan after**: Run `terraform plan` after state changes to verify
3. **One at a time**: Make incremental changes, not bulk operations
4. **Test in non-prod**: Practice state operations in dev/test first
5. **Version control**: Commit code changes with state operations

## Common Operations

### 1. Import Existing Resources

**Workflow:**

```bash
# Step 1: Write the resource configuration in .tf file
# Step 2: Import the resource
terraform import <resource_address> <resource_id>

# Step 3: Verify with plan (should show no changes)
terraform plan
```

**Example - Import AWS S3 Bucket:**

```hcl
# 1. Add to main.tf
resource "aws_s3_bucket" "existing" {
  bucket = "my-existing-bucket"
}

# 2. Import
terraform import aws_s3_bucket.existing my-existing-bucket

# 3. Verify
terraform plan  # Should show: No changes
```

**Tips:**
- Find resource ID format in provider docs
- Import one resource at a time
- Some resources require multiple imports (bucket + versioning + encryption)
- Use `terraform show` to see imported attributes

### 2. Move Resources (Rename/Refactor)

**Workflow:**

```bash
# Move resource to new address
terraform state mv <source> <destination>

# Verify
terraform plan  # Should show: No changes
```

**Example - Rename Resource:**

```bash
# Before: aws_s3_bucket.bucket
# After:  aws_s3_bucket.main

terraform state mv aws_s3_bucket.bucket aws_s3_bucket.main
```

**Example - Move to Module:**

```bash
# Move resource into module
terraform state mv aws_instance.web module.web_server.aws_instance.main
```

**Example - Move Between Modules:**

```bash
terraform state mv module.old.aws_db_instance.main module.new.aws_db_instance.main
```

**Tips:**
- Update .tf files to match new addresses before running plan
- Use quotes for addresses with special characters
- Move related resources together (e.g., bucket + bucket_policy)

### 3. Remove from State

**Workflow:**

```bash
# Remove resource from state (keeps actual resource)
terraform state rm <resource_address>

# Verify resource still exists in cloud
# Update .tf files to remove resource definition
```

**Example - Remove Resource:**

```bash
# Remove from Terraform management
terraform state rm aws_s3_bucket.temp

# Resource still exists in AWS, just not managed by Terraform
```

**Use cases:**
- Handing off resource to another Terraform workspace
- Removing accidentally imported resources
- Decomissioning Terraform management of legacy resources

### 4. Migrate State Backend

**Workflow for Local → S3:**

```hcl
# Step 1: Create S3 bucket and DynamoDB table for locking

# Step 2: Add backend configuration
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "project/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

# Step 3: Initialize with migration
terraform init -migrate-state

# Step 4: Verify
terraform plan
```

**Backend Migration Checklist:**
- [ ] Backup current state file
- [ ] Create new backend resources (S3 bucket, etc.)
- [ ] Update backend configuration in code
- [ ] Run `terraform init -migrate-state`
- [ ] Verify with `terraform plan`
- [ ] Test state locking works
- [ ] Update team documentation

### 5. List State Resources

**View all resources in state:**

```bash
# List all resources
terraform state list

# Show specific resource details
terraform state show aws_s3_bucket.main

# Output state as JSON
terraform show -json
```

## Troubleshooting

### State Lock Issues

```bash
# If state is locked and shouldn't be
terraform force-unlock <lock-id>

# Only use if you're certain no other operation is running
```

### State Drift Detection

```bash
# Check for drift between state and actual infrastructure
terraform plan -refresh-only

# Update state to match reality (doesn't change infrastructure)
terraform apply -refresh-only
```

### Corrupted State Recovery

```bash
# Terraform keeps backups automatically
ls terraform.tfstate.backup

# Restore from backup
cp terraform.tfstate.backup terraform.tfstate

# Or restore from remote backend version history (S3 versioning)
```

## Pre-Operation Checklist

Before any state operation:
- [ ] Backup state file (`cp terraform.tfstate terraform.tfstate.backup`)
- [ ] Ensure no other operations are running
- [ ] Understand the exact command and its impact
- [ ] Have rollback plan ready
- [ ] Test in non-production first

## Post-Operation Verification

After any state operation:
- [ ] Run `terraform plan` (should show expected changes or no changes)
- [ ] Verify resource still exists in cloud console
- [ ] Check state file size is reasonable
- [ ] Commit changes to version control
- [ ] Document what was done and why

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/armanzeroeight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
