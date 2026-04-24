---
name: deployment-runbook-creation
description: Generate deployment runbook documentation following the DEPLOYMENT-RUNBOOK template. Use when planning deployments, documenting procedures, or when the user asks for a runbook. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Deployment Runbook Creation Skill

> **Purpose:** Generate deployment runbook documentation. Ensures safe, repeatable deployments with rollback procedures.

## Trigger

**When:** New release being prepared OR user requests deployment documentation
**Context Needed:** Services, environments, dependencies
**MCP Tools:** `container-tools_get-config`, `read_file`

## Required Sections

```markdown
# [Service/Feature] - Deployment Runbook

## Overview

- Service: [name]
- Version: [version]
- Environment: [dev/staging/prod]

## Pre-Deployment Checklist

- [ ] All tests passing
- [ ] Database migrations ready
- [ ] Config changes documented
- [ ] Monitoring alerts configured

## Deployment Steps

1. [step]
2. [step]

## Rollback Procedure

1. [step]
2. [step]

## Monitoring

- Health check: [URL]
- Logs: [location]
- Alerts: [channel]
```

## Environment-Specific Sections

### Development

````markdown
## Dev Deployment

```bash
bun run deploy:dev
```
````

`````

- Auto-deploys from `develop` branch
- No approval required

````

### Staging
```markdown
## Staging Deployment
```bash
bun run deploy:staging
````

- Requires PR approval
- Runs integration tests

````

### Production
```markdown
## Production Deployment
```bash
bun run deploy:prod
````

- Requires 2 approvals
- Maintenance window: [time]
- Rollback within: 15 minutes

````

## Contact Information

```markdown
## On-Call

| Role | Name | Contact |
|:-----|:-----|:--------|
| Primary | @oncall | [channel] |
| Secondary | @backup | [channel] |
| Escalation | @lead | [channel] |
````

## Reference

- [08-DEPLOYMENT-RUNBOOK-TEMPLATE.md](/docs/templates/08-DEPLOYMENT-RUNBOOK-TEMPLATE.md)
- [DOCKER-GUIDE.md](/docs/technical/infrastructure/DOCKER-GUIDE.md)

```

`````

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
