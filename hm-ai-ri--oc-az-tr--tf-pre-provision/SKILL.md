---
name: tf-pre-provision
description: Complete provisioning workflow with pre-checks and post-validation using specialized agents Use when this capability is needed.
metadata:
  author: hm-ai-ri
---

## What I Do

This skill defines the COMPLETE provisioning workflow that MUST be followed for any `terraform apply` or `terraform destroy` command. It orchestrates multiple specialized agents to ensure safe, secure, compliant, and VERIFIED infrastructure deployments.

## When to Use Me

**ALWAYS** when provisioning or destroying Azure resources with Terraform.

---

# PHASE 1: PRE-PROVISIONING CHECKS

## Step 1: Validation (@tf-validator)

Invoke the validation agent to check configuration syntax:

```
@tf-validator Please validate the Terraform configuration in this project
```

**Must Pass:** terraform fmt, terraform validate, variables have values

---

## Step 2: Security Audit (@tf-security-auditor)

Invoke the security auditor to check for vulnerabilities:

```
@tf-security-auditor Please perform a security audit on the Terraform configuration
```

**If CRITICAL issues found:** Do NOT proceed.

---

## Step 3: Cost Estimation (@tf-cost-estimator)

Estimate costs before provisioning:

```
@tf-cost-estimator Please estimate the costs for this Terraform configuration
```

---

## Step 4: Compliance Check (@tf-compliance-checker)

Verify compliance with standards:

```
@tf-compliance-checker Please check this configuration against CIS benchmarks
```

---

## Step 5: Plan Review (@tf-plan-reviewer)

Review the terraform plan:

```bash
terraform plan -out=tfplan
```

Then:

```
@tf-plan-reviewer Please review this terraform plan output for safety
```

---

## Step 6: User Approval

**MANDATORY:** Get explicit user confirmation before applying.

---

# PHASE 2: EXECUTION

## Step 7: Apply or Destroy

```bash
terraform apply -auto-approve  # Only after user approval
# OR
terraform destroy -auto-approve  # Only after user approval
```

---

# PHASE 3: POST-PROVISIONING VALIDATION (MANDATORY)

## Step 8: Azure Resource Validation (@tf-azure-validator)

**ALWAYS RUN** after every `terraform apply` or `terraform destroy`:

### After Apply:
```
@tf-azure-validator Validate these resources exist in Azure: [resource names]
```

### After Destroy:
```
@tf-azure-validator Validate these resources were deleted from Azure: [resource names]
```

---

## Complete Workflow Checklist

### Pre-Provisioning
- [ ] Step 1: Validation passed (@tf-validator)
- [ ] Step 2: No CRITICAL security issues (@tf-security-auditor)
- [ ] Step 3: Cost estimate acceptable (@tf-cost-estimator)
- [ ] Step 4: Compliance verified (@tf-compliance-checker)
- [ ] Step 5: Plan reviewed (@tf-plan-reviewer)
- [ ] Step 6: User approved

### Execution
- [ ] Step 7: terraform apply/destroy completed

### Post-Provisioning
- [ ] Step 8: Resources verified in Azure (@tf-azure-validator)

---

## Agent Summary

| Agent | Phase | Purpose |
|-------|-------|---------|
| @tf-validator | Pre | Syntax validation |
| @tf-security-auditor | Pre | Security scanning |
| @tf-cost-estimator | Pre | Cost estimation |
| @tf-compliance-checker | Pre | Compliance checking |
| @tf-plan-reviewer | Pre | Plan risk assessment |
| @tf-azure-validator | Post | Azure API verification |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hm-ai-ri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
