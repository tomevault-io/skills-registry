---
name: iac-skill
description: Infrastructure as Code with Terraform, Ansible, and CloudFormation. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Infrastructure as Code Skill

## Overview
Master IaC with Terraform, Ansible, and CloudFormation for automated infrastructure.

## Parameters
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| tool | string | No | terraform | IaC tool |
| operation | string | Yes | - | Operation type |

## Core Topics

### MANDATORY
- Terraform HCL syntax and providers
- State management and locking
- Modules and workspaces
- Ansible playbooks and roles
- Inventory management

### OPTIONAL
- CloudFormation templates
- Pulumi and CDK
- Testing IaC (terratest)
- Secret management

### ADVANCED
- Custom providers
- Complex module design
- Multi-cloud strategies
- Drift detection

## Quick Reference

```bash
# Terraform
terraform init
terraform plan -out=plan.tfplan
terraform apply plan.tfplan
terraform destroy
terraform fmt -recursive
terraform validate
terraform state list
terraform import aws_instance.web i-123

# State Management
terraform state mv old new
terraform state rm resource
terraform force-unlock LOCK_ID

# Ansible
ansible-playbook -i inventory playbook.yml
ansible-playbook playbook.yml --check --diff
ansible-playbook playbook.yml --tags nginx
ansible all -m ping -i inventory
ansible-vault encrypt secrets.yml
```

## Troubleshooting

### Common Failures
| Symptom | Root Cause | Solution |
|---------|------------|----------|
| State lock | Concurrent ops | Wait or force-unlock |
| Resource exists | Drift | Import or delete |
| Provider auth | Credentials | Check AWS_PROFILE |
| Cycle error | Dependencies | Restructure |

### Debug Checklist
1. Validate: `terraform validate`
2. Check state: `terraform state list`
3. Debug: `TF_LOG=DEBUG terraform plan`
4. Verify credentials

### Recovery Procedures

#### Corrupted State
1. Restore from S3 versioning
2. Or import: `terraform import` for each resource

## Resources
- [Terraform Docs](https://developer.hashicorp.com/terraform/docs)
- [Ansible Docs](https://docs.ansible.com)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
