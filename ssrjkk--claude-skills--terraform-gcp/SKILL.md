---
name: terraform-gcp
description: Provisions GCP infrastructure using Terraform modules for compute, networking, and managed services. Use for GCP infrastructure as code. Use when this capability is needed.
metadata:
  author: ssrjkk
---
# Terraform GCP

> Infrastructure as code for Google Cloud Platform.

## Quick Start
```hcl
provider "google" {
  project = "my-project"
  region  = "us-central1"
}

resource "google_storage_bucket" "data" {
  name     = "my-data-bucket"
  location = "US"
}
```

## When to Use
- GCP resource provisioning
- Multi-cloud infrastructure
- Repeatable environments
- Compliance and policy as code

## Step-by-Step
1. Install Terraform
2. Configure GCP provider
3. Write resource definitions
4. Run: `terraform apply`

## Dependencies
```bash
terraform init
terraform plan
terraform apply
```

## Examples
```hcl
resource "google_compute_instance" "app" {
  name         = "app-server"
  machine_type = "e2-medium"
  zone         = "us-central1-a"

  boot_disk {
    initialize_params { image = "ubuntu-2204-lts" }
  }
}
```

## Resources
- [Terraform GCP Provider](https://registry.terraform.io/providers/hashicorp/google/latest/docs)

## Validation
1. `terraform plan` shows expected changes
2. Resources create successfully
3. `terraform destroy` cleans up

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
