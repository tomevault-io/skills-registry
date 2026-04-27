---
name: cloud-architect
description: Expert cloud architecture including AWS, GCP, Azure design patterns, cost optimization, and multi-cloud strategies Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Cloud Architect

## Purpose
Design optimal cloud architectures including service selection, cost optimization, multi-cloud strategies, and cloud-native patterns.

## Activation Keywords
- cloud architecture, AWS, GCP, Azure
- serverless, Lambda, Cloud Functions
- cost optimization, FinOps
- multi-cloud, hybrid cloud
- well-architected, cloud-native

## Core Capabilities

### 1. Service Selection
- Compute options
- Storage tiers
- Database services
- Networking
- Managed services

### 2. Architecture Patterns
- Serverless
- Container-based
- Hybrid cloud
- Multi-region
- Event-driven

### 3. Cost Optimization
- Right-sizing
- Reserved capacity
- Spot instances
- Auto-scaling
- Cost allocation

### 4. Security
- IAM design
- Network security
- Encryption
- Compliance
- Audit

### 5. Reliability
- Multi-AZ
- Multi-region
- Disaster recovery
- Backup strategies
- SLA design

## Service Comparison

| Service | AWS | GCP | Azure |
|---------|-----|-----|-------|
| Compute | EC2, Lambda | GCE, Cloud Functions | VMs, Functions |
| Container | EKS, ECS | GKE | AKS |
| Database | RDS, DynamoDB | Cloud SQL, Firestore | SQL DB, Cosmos |
| Storage | S3 | Cloud Storage | Blob Storage |
| Queue | SQS | Pub/Sub | Service Bus |

## Well-Architected Pillars

```
1. Operational Excellence
   - Automation
   - Monitoring
   - Incident response

2. Security
   - IAM
   - Encryption
   - Network security

3. Reliability
   - Multi-AZ
   - Auto-scaling
   - Backup/DR

4. Performance Efficiency
   - Right-sizing
   - Caching
   - CDN

5. Cost Optimization
   - Reserved capacity
   - Spot instances
   - Cleanup unused

6. Sustainability
   - Efficient resources
   - Managed services
   - Region selection
```

## Cost Optimization Checklist

```markdown
## Compute
- [ ] Right-size instances
- [ ] Use Spot/Preemptible for batch
- [ ] Reserved instances for baseline
- [ ] Auto-scaling configured

## Storage
- [ ] Lifecycle policies
- [ ] Intelligent tiering
- [ ] Delete unused snapshots

## Networking
- [ ] NAT Gateway optimization
- [ ] Data transfer costs
- [ ] CDN for static content

## Database
- [ ] Reserved capacity
- [ ] Read replicas vs scaling
- [ ] Serverless for variable workloads

## Monitoring
- [ ] Cost alerts
- [ ] Budget tracking
- [ ] Resource tagging
```

## Example Usage

```
User: "Design AWS architecture for a SaaS application"

Cloud Architect Response:
1. Compute
   - EKS for main application
   - Lambda for async processing
   - Auto-scaling groups

2. Data
   - Aurora PostgreSQL (Multi-AZ)
   - ElastiCache Redis
   - S3 for object storage

3. Networking
   - VPC with public/private subnets
   - ALB for load balancing
   - CloudFront CDN

4. Security
   - IAM roles per service
   - Secrets Manager
   - WAF on CloudFront

5. Cost optimization
   - Reserved instances for baseline
   - Spot for batch jobs
   - S3 Intelligent Tiering
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
