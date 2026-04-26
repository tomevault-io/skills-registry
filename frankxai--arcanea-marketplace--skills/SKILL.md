---
name: oracle-intelligence
description: Oracle Cloud Infrastructure expertise for enterprise AI and cloud architecture Use when this capability is needed.
metadata:
  author: frankxai
---

# Oracle Intelligence
## Enterprise AI & Cloud Architecture Expertise

Professional guidance for Oracle Cloud Infrastructure (OCI), enterprise AI systems, and scalable architecture patterns.

## Core Expertise Areas

### 1. Oracle Cloud Infrastructure (OCI)
- Compute and container services
- Networking and security
- Database services (Autonomous DB, MySQL, PostgreSQL)
- Object storage and file systems
- Identity and access management

### 2. Oracle AI Services
- OCI Generative AI
- AI Language and Vision services
- Anomaly Detection
- Document Understanding
- Custom model training

### 3. Enterprise Architecture
- Multi-region deployment patterns
- High availability design
- Disaster recovery strategies
- Cost optimization
- Security best practices

### 4. Integration Patterns
- API Gateway and management
- Event-driven architectures
- Microservices on OKE (Kubernetes)
- Functions (serverless)
- Integration Cloud

## Architecture Patterns

### Basic 3-Tier
```
Internet → Load Balancer → Web Tier → App Tier → DB Tier
                                                    ↓
                                              Autonomous DB
```

### AI-Enhanced Application
```
User Request → API Gateway → AI Services → Business Logic
                    ↓              ↓
               Gen AI          Vision/NLP
                    ↓              ↓
              Response Generation
```

### Event-Driven Microservices
```
Event Sources → Streaming → Functions → Services
      ↓                          ↓
   Queue                   Kubernetes (OKE)
      ↓                          ↓
   Storage                  Databases
```

## Best Practices

### Security
- Use compartments for isolation
- Implement least-privilege IAM
- Encrypt data at rest and in transit
- Enable audit logging
- Regular security assessments

### Cost Optimization
- Right-size compute instances
- Use preemptible instances for batch
- Leverage reserved capacity
- Implement auto-scaling
- Monitor with Cost Analysis

### Performance
- Use Autonomous DB for self-tuning
- Implement caching (Redis, Memcached)
- CDN for static content
- Regional load balancing
- Connection pooling

## OCI CLI Quick Reference

```bash
# List instances
oci compute instance list --compartment-id $C

# Create Autonomous DB
oci db autonomous-database create \
  --compartment-id $C \
  --db-name mydb \
  --cpu-core-count 1 \
  --data-storage-size-in-tbs 1

# Deploy to Kubernetes
oci ce cluster create-kubeconfig --cluster-id $CLUSTER_ID
kubectl apply -f deployment.yaml
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/frankxai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
