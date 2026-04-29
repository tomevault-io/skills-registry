---
name: cloud-skill
description: Cloud infrastructure with AWS, Azure, GCP - architecture, services, security, and cost optimization. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Cloud Infrastructure Skill

## Overview
Master cloud platforms: AWS, Azure, and GCP.

## Parameters
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| provider | string | No | aws | Cloud provider |
| service | string | No | compute | Service type |

## Core Topics

### MANDATORY
- AWS: EC2, S3, RDS, Lambda, VPC
- Azure: VMs, Storage, AKS
- GCP: Compute Engine, GKE
- IAM and security
- Networking (VPCs, subnets)

### OPTIONAL
- Cost optimization
- Multi-cloud strategies
- Managed Kubernetes
- Serverless patterns

### ADVANCED
- Well-Architected Framework
- Landing zones
- Organizations/Control Tower
- FinOps

## Service Comparison
| Category | AWS | Azure | GCP |
|----------|-----|-------|-----|
| Compute | EC2 | VMs | Compute Engine |
| K8s | EKS | AKS | GKE |
| Serverless | Lambda | Functions | Cloud Functions |
| Storage | S3 | Blob | Cloud Storage |

## Quick Reference

```bash
# AWS CLI
aws sts get-caller-identity
aws ec2 describe-instances
aws s3 ls s3://bucket-name
aws eks update-kubeconfig --name cluster

# Azure CLI
az login
az account list
az vm list
az aks get-credentials --name cluster

# GCP CLI
gcloud auth login
gcloud projects list
gcloud compute instances list
gcloud container clusters get-credentials cluster
```

## Troubleshooting

### Common Failures
| Symptom | Root Cause | Solution |
|---------|------------|----------|
| Access Denied | IAM policy | Check policies |
| Quota Exceeded | Service limit | Request increase |
| Timeout | Network/SG | Check VPC, SGs |
| Cost spike | Runaway resources | Cost Explorer |

### Debug Checklist
1. Identity: `aws sts get-caller-identity`
2. Region: `echo $AWS_REGION`
3. Permissions: Check IAM
4. CloudTrail: Audit logs

### Recovery Procedures

#### Compromised Key
1. Disable key immediately
2. Review CloudTrail
3. Rotate credentials

## Resources
- [AWS Docs](https://docs.aws.amazon.com)
- [Azure Docs](https://docs.microsoft.com/azure)
- [GCP Docs](https://cloud.google.com/docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
