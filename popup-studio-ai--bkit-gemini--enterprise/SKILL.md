---
name: enterprise
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Enterprise Skill

> Enterprise-grade systems with microservices, Kubernetes, and Terraform

## Overview

The Enterprise skill provides guidance for building large-scale, production-ready systems with microservices architecture, container orchestration, and infrastructure-as-code.

## When to Use

- High-traffic applications
- Multi-team development
- Complex business logic
- Regulatory compliance needs
- Custom infrastructure requirements

## Architecture Patterns

### Monorepo Structure

```
enterprise-project/
├── packages/
│   ├── api-gateway/
│   ├── user-service/
│   ├── order-service/
│   ├── payment-service/
│   └── shared/
├── infrastructure/
│   ├── terraform/
│   └── kubernetes/
├── docs/
│   ├── 01-plan/
│   ├── 02-design/
│   ├── 03-analysis/
│   └── 04-report/
├── turbo.json
└── package.json
```

### Key Technologies

- **Container**: Docker, Kubernetes
- **IaC**: Terraform, Pulumi
- **CI/CD**: GitHub Actions, ArgoCD
- **Observability**: Prometheus, Grafana, Jaeger
- **Service Mesh**: Istio, Linkerd

## Key Phases (Enterprise Level)

| Phase | Required | Description |
|-------|----------|-------------|
| 1. Schema | ✅ | Domain modeling |
| 2. Convention | ✅ | Team coding standards |
| 3. Mockup | ✅ | UI/UX prototypes |
| 4. API | ✅ | API contracts (OpenAPI) |
| 5. Design System | ✅ | Shared component library |
| 6. UI Integration | ✅ | Frontend integration |
| 7. SEO/Security | ✅ | Security hardening |
| 8. Review | ✅ | Architecture review |
| 9. Deployment | ✅ | Multi-env deployment |

## Getting Started

```bash
# Initialize Enterprise project
/enterprise init

# Full pipeline with all phases
/development-pipeline start
```

## Infrastructure Example

```hcl
# infrastructure/terraform/main.tf
module "eks" {
  source  = "terraform-aws-modules/eks/aws"
  version = "~> 19.0"

  cluster_name    = "enterprise-cluster"
  cluster_version = "1.28"

  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
