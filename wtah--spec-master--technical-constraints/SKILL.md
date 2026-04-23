---
name: technical-constraints
description: Standardized structure for capturing technical constraints including technology stack, infrastructure, and integrations. Input for architecture agents. Located in .constraints/ directory. Use when this capability is needed.
metadata:
  author: wtah
---

# Technical Constraints Skill

This skill defines the **standardized structure** for documenting technical constraints. The `.constraints/` directory captures all technical boundaries and limitations that architecture agents must respect.

---

## Core Concept

The `.constraints/` directory captures:
1. **Technology stack** - Available and preferred technologies
2. **Infrastructure** - Cloud, hosting, network constraints
3. **Integrations** - External systems that must be used

This is **input** for the architecture process - it constrains design decisions.

---

## Directory Structure

```
.constraints/
├── TECHNOLOGY.md               # Technology stack constraints
├── INFRASTRUCTURE.md           # Infrastructure and deployment constraints
└── INTEGRATIONS.md             # External system integration requirements
```

---

## File Templates

### `TECHNOLOGY.md` - Technology Stack Constraints

```markdown
# Technology Constraints - {Project Name}

## Overview
This document defines technology constraints for the project, including required technologies, preferred options, and technologies to avoid.

---

## Technology Stack Summary

| Layer | Required | Preferred | Avoid |
|-------|----------|-----------|-------|
| Frontend | {Required tech} | {Preferred options} | {Forbidden} |
| Backend | {Required tech} | {Preferred options} | {Forbidden} |
| Database | {Required tech} | {Preferred options} | {Forbidden} |

---

## Languages

### Required Languages
| Language | Version | Scope | Rationale |
|----------|---------|-------|-----------|
| {Language} | {Version} | {Where used} | {Why required} |

### Preferred Languages
| Language | Version | Scope | Rationale |
|----------|---------|-------|-----------|
| {Language} | {Version} | {Where preferred} | {Why preferred} |

### Forbidden Languages
| Language | Reason |
|----------|--------|
| {Language} | {Why forbidden} |

---

## Frameworks & Libraries

### Frontend
| Category | Required | Preferred | Forbidden |
|----------|----------|-----------|-----------|
| UI Framework | {e.g., React 18+} | {Options} | {e.g., Angular} |
| State Management | {Required} | {Options} | {Forbidden} |
| Testing | {Required} | {Options} | {Forbidden} |

### Backend
| Category | Required | Preferred | Forbidden |
|----------|----------|-----------|-----------|
| Framework | {e.g., Express} | {Options} | {Forbidden} |
| ORM/ODM | {Required} | {Options} | {Forbidden} |
| Testing | {Required} | {Options} | {Forbidden} |

---

## Databases

### Primary Database
| Attribute | Constraint |
|-----------|------------|
| **Type** | {Relational / Document / Graph / etc.} |
| **Product** | {e.g., PostgreSQL 15+} |
| **Hosting** | {Managed / Self-hosted} |
| **Rationale** | {Why this database} |

### Secondary Databases (if any)
| Purpose | Type | Product | Rationale |
|---------|------|---------|-----------|
| Caching | Key-Value | {e.g., Redis} | {Why} |
| Search | Document | {e.g., Elasticsearch} | {Why} |

---

## API Standards

| Aspect | Constraint |
|--------|------------|
| **Style** | {REST / GraphQL / gRPC} |
| **Format** | {JSON / Protocol Buffers} |
| **Versioning** | {URL / Header / Query} |
| **Documentation** | {OpenAPI / GraphQL Schema} |
| **Authentication** | {OAuth2 / JWT / API Key} |

---

## Licensing Constraints

| License Type | Allowed | Forbidden |
|--------------|---------|-----------|
| MIT/Apache/BSD | Yes | - |
| GPL | - | Yes |
| Commercial | Case by case | - |
| AGPL | - | Yes |

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| {Date} | Initial version | {Author} |
```

### `INFRASTRUCTURE.md` - Infrastructure Constraints

```markdown
# Infrastructure Constraints - {Project Name}

## Overview
This document defines infrastructure constraints including cloud provider, hosting requirements, and deployment limitations.

---

## Cloud Provider

### Primary Provider
| Attribute | Value |
|-----------|-------|
| **Provider** | {AWS / Azure / GCP / Other} |
| **Regions** | {Allowed regions} |
| **Rationale** | {Why this provider} |

### Multi-Cloud Policy
- **Allowed**: {Yes / No / Specific cases}
- **Constraints**: {If allowed, what constraints}

---

## Compute Constraints

### Container Platform
| Attribute | Constraint |
|-----------|------------|
| **Platform** | {Kubernetes / ECS / Cloud Run / etc.} |
| **Version** | {Minimum version} |
| **Managed** | {Yes / No} |

### Serverless Constraints
| Attribute | Constraint |
|-----------|------------|
| **Allowed** | {Yes / No / Specific use cases} |
| **Services** | {Lambda / Cloud Functions / etc.} |

---

## Network Constraints

| Attribute | Constraint |
|-----------|------------|
| **VPC** | {Use existing / Create new} |
| **Internet Egress** | {Allowed / NAT only / Forbidden} |
| **VPN Required** | {Yes / No} |

---

## Storage Constraints

### Object Storage
| Attribute | Constraint |
|-----------|------------|
| **Service** | {S3 / GCS / Azure Blob} |
| **Encryption** | {Requirements} |

### Block Storage
| Attribute | Constraint |
|-----------|------------|
| **Types** | {Allowed types} |
| **Encryption** | {Requirements} |

---

## Environment Requirements

| Environment | Purpose | Required |
|-------------|---------|----------|
| Development | Individual dev | Yes |
| Staging | Pre-prod testing | Yes |
| Production | Live system | Yes |

---

## Availability Requirements

| Environment | Target | Maximum Downtime |
|-------------|--------|------------------|
| Production | {e.g., 99.9%} | {Per month} |
| Staging | {e.g., 99%} | {Per month} |

---

## CI/CD Constraints

| Aspect | Constraint |
|--------|------------|
| **Platform** | {GitHub Actions / GitLab CI / Jenkins / etc.} |
| **Deployment Strategy** | {Blue-green / Canary / Rolling} |

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| {Date} | Initial version | {Author} |
```

### `INTEGRATIONS.md` - External System Integrations

```markdown
# Integration Constraints - {Project Name}

## Overview
This document defines external systems that must be integrated and constraints around those integrations.

---

## Integration Summary

| System | Type | Direction | Priority | Status |
|--------|------|-----------|----------|--------|
| {System} | {API/Event/File} | {Inbound/Outbound/Both} | Must/Should | Existing/New |

---

## Required Integrations

### {Integration Name}

| Attribute | Value |
|-----------|-------|
| **System** | {External system name} |
| **Owner** | {Team/Vendor responsible} |
| **Type** | {REST API / SOAP / GraphQL / Event / File} |
| **Direction** | {Inbound / Outbound / Bidirectional} |
| **Priority** | Must / Should / Could |

#### Connection Details
| Attribute | Value |
|-----------|-------|
| **Endpoint** | {URL or connection string} |
| **Protocol** | {HTTPS / SFTP / etc.} |
| **Authentication** | {OAuth / API Key / Certificate} |
| **Rate Limits** | {Requests per second/minute} |

#### Data Exchange
| Data | Direction | Format | Frequency |
|------|-----------|--------|-----------|
| {Data type} | In/Out | {JSON/XML/CSV} | {Real-time/Batch} |

#### Constraints
- **Availability**: {SLA of external system}
- **Latency**: {Expected response time}

#### Fallback Behavior
{What to do when integration is unavailable}

---

### {Another Integration}

{Same structure as above}

---

## Authentication & Credentials

### Credential Management
| Aspect | Requirement |
|--------|-------------|
| **Storage** | {Vault / Secrets Manager / etc.} |
| **Rotation** | {Rotation policy} |

### SSO/Identity Provider (if required)
| Attribute | Value |
|-----------|-------|
| **Provider** | {Okta / Auth0 / Azure AD / etc.} |
| **Protocol** | {SAML / OIDC / etc.} |

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| {Date} | Initial version | {Author} |
```

---

## Relationship to Architecture

The `.constraints/` directory provides **boundaries** for the architecture process:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    .product/    │────▶│                 │────▶│ .agent-registry/│
│  (Requirements) │     │   Architecture  │     │    (Agents)     │
└─────────────────┘     │     Process     │     └─────────────────┘
                        │                 │
┌─────────────────┐     │   ┌─────────┐   │     ┌─────────────────┐
│  .constraints/  │────▶│   │ Design  │   │────▶│     .specs/     │
│  (Boundaries)   │     │   │Decisions│   │     │ (Specifications)│
└─────────────────┘     │   └─────────┘   │     └─────────────────┘
                        └─────────────────┘
```

### How Agents Use Constraints

| Agent | Uses From .constraints/ |
|-------|-------------------------|
| `high-level-architect` | All constraints for System Context decisions |
| `container-architect` | TECHNOLOGY.md for component technology choices |
| `component-architect` | TECHNOLOGY.md for implementation patterns |
| `deployment-architect` | INFRASTRUCTURE.md for deployment design |
| `dynamic-flow-architect` | INTEGRATIONS.md for external flow design |

---

## Best Practices

### DO:
1. **Document the "why"** - Explain rationale for constraints
2. **Distinguish hard vs soft** - Know what's negotiable
3. **Keep updated** - Constraints change over time
4. **Be specific** - Versions, limits, requirements

### DON'T:
1. Invent constraints - Document real limitations
2. Over-constrain - Leave room for design
3. Forget integrations - External systems are real constraints
4. Skip rationale - Future you will wonder why

---

## Checklist: Constraints Readiness

Before running `/init`, ensure:

- [ ] TECHNOLOGY.md has language/framework constraints
- [ ] TECHNOLOGY.md has database constraints
- [ ] INFRASTRUCTURE.md has cloud/hosting constraints
- [ ] INFRASTRUCTURE.md has environment requirements
- [ ] INTEGRATIONS.md lists required external systems
- [ ] All constraints have rationale documented

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
