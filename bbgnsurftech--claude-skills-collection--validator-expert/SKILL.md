---
name: validator-expert
description: Validate production readiness of Vertex AI Agent Engine deployments across Use when this capability is needed.
metadata:
  author: bbgnsurftech
---
## What This Skill Does

Production validator for Vertex AI deployments. Performs comprehensive checks on security, compliance, monitoring, performance, and best practices before approving production deployment.

## When This Skill Activates

Triggers: "validate deployment", "production readiness", "security audit vertex ai", "check compliance", "validate adk agent"

## Validation Checklist

### Security Validation
- ✅ IAM roles follow least privilege
- ✅ VPC Service Controls enabled
- ✅ Encryption at rest configured
- ✅ No hardcoded secrets
- ✅ Service accounts properly configured
- ✅ Model Armor enabled (for ADK)

### Monitoring Validation
- ✅ Cloud Monitoring dashboards configured
- ✅ Alerting policies set
- ✅ Token usage tracking enabled
- ✅ Error rate monitoring active
- ✅ Latency SLOs defined

### Performance Validation
- ✅ Auto-scaling configured
- ✅ Resource limits appropriate
- ✅ Caching strategy implemented
- ✅ Code Execution sandbox TTL set
- ✅ Memory Bank retention configured

### Compliance Validation
- ✅ Audit logging enabled
- ✅ Data residency requirements met
- ✅ Privacy policies implemented
- ✅ Backup/disaster recovery configured

## Tool Permissions

Read, Grep, Glob, Bash - Read-only analysis for security

## References

- Vertex AI Security: https://cloud.google.com/vertex-ai/docs/security

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bbgnsurftech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
