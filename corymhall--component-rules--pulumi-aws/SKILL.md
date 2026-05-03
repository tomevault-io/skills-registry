---
name: pulumi-aws
description: Configures AWS infrastructure with Pulumi TypeScript. Applies best practices for RDS Aurora Serverless v2, S3 buckets, IAM roles. Use when writing Pulumi code for AWS, reviewing infrastructure, or asking about Aurora capacity, S3 encryption, or IAM policies.
metadata:
  author: corymhall
---

# AWS Infrastructure Rules for Pulumi

## Domains

**RDS** - Aurora Serverless v2 capacity, engine support, promotion tiers, mixed clusters
  See [rds/](rds/)

**S3** - Encryption, versioning, lifecycle rules
  See [s3/](s3/) *(planned)*

**IAM** - Connecting resources, least privilege policies
  See [iam/](iam/)

## Quick Reference

### RDS

- `capacity` - ACU ranges, scaling behavior, auto-pause configuration
- `engine-support` - Serverless v2 compatible engine versions
- `workload-shaping` - Choosing provisioned vs serverless by workload pattern
- `resource-mapping` - Pulumi resource structure, writer ordering, instance classes
- `promotion-tiers` - Failover priority, tier 0-1 vs 2-15 scaling behavior
- `mixed-instances` - Combining provisioned and serverless in one cluster

### IAM

- `connecting-resources` - Service-to-service IAM permissions matrix
- Individual connection rules: `lambda-to-dynamodb`, `eventbridge-to-lambda`, `sns-to-sqs`, etc.

## Workflow

1. Identify AWS service being configured
2. Read relevant domain rules
3. Apply constraints and patterns
4. Validate with `pulumi preview`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/corymhall) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
