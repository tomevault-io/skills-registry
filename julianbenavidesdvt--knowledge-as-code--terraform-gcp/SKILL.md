---
name: terraform-gcp
description: Manage Google Cloud Platform infrastructure using Terraform for Infrastructure as Code (IaC). Use when this capability is needed.
metadata:
  author: julianbenavidesdvt
---

# Terraform GCP Manager

This skill provides a standardized workflow for managing GCP infrastructure using Terraform.

## Prerequisites

- [Terraform](https://www.terraform.io/downloads.html) installed.
- [Google Cloud SDK](https://cloud.google.com/sdk/docs/install) installed and authenticated.
- A GCP Project ID and appropriate permissions.

## Process

1.  **Initialize Workflow**:
    *   Navigate to the infrastructure directory (e.g., `infrastructure/` or `terraform/`).
    *   Run `terraform init` to initialize the working directory and install providers.

2.  **Configuration Management**:
    *   Check for `provider.tf`, `variables.tf`, and `main.tf`.
    *   Ensure the `google` provider is configured with the correct project, region, and zone.
    *   Manage sensitive data using `.tfvars` (ensure they are in `.gitignore`).

3.  **Planning and Validation**:
    *   Run `terraform validate` to check configuration syntax.
    *   Run `terraform plan -out=tfplan` to preview changes and save the execution plan.

4.  **Application**:
    *   Run `terraform apply "tfplan"` to apply the changes.
    *   Always verify the output for any errors or resource limits.

5.  **State Management**:
    *   Prefer remote state (GCS backend) for collaboration.
    *   Ensure state files are never committed to version control.

## Best Practices

- **Modules**: Use Terraform modules for reusable infrastructure components.
- **Naming**: Use consistent naming conventions for GCP resources.
- **Tags/Labels**: Always add labels to resources for cost tracking and management.
- **Least Privilege**: Use specific Service Accounts for Terraform with only the necessary IAM roles.
- **Documentation**: Use the `context7_manager` skill to document the infrastructure changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
