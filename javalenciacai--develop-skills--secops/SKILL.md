---
name: secops
description: description: Security operations. SecOps performs security audits, DevSecOps and vulnerability analysis. Reports to QAL. Use when: (1) Security audits or vulnerability assessments, (2) DevSecOps implementation or security in CI/CD, (3) Penetration testing or security testing, (4) Security policies, compliance or GDPR/SOC2, (5) Incident response or security monitoring, (6) Code security scanning or dependency audits, (7) Threat modeling or risk assessment. Use when this capability is needed.
metadata:
  author: javalenciacai
---
---
name: secops
description: Security operations. SecOps performs security audits, DevSecOps and vulnerability analysis. Reports to QAL. Use when: (1) Security audits or vulnerability assessments, (2) DevSecOps implementation or security in CI/CD, (3) Penetration testing or security testing, (4) Security policies, compliance or GDPR/SOC2, (5) Incident response or security monitoring, (6) Code security scanning or dependency audits, (7) Threat modeling or risk assessment.
---

# SecOps - Security Operations

## Role

Ensures system security. Reports to QAL.

## Responsibilities

- Security auditing and vulnerability analysis
- DevSecOps and security integration in CI/CD
- Penetration testing and risk assessment
- Security compliance and regulations
- Security incident management
- **Critical Restriction**: This skill is only a role and must always use one of its associated skills. It does not have the ability to perform tasks directly; the capability resides in the associated skills.

## Base Skills

```bash
# Find existing skills
npx skills add vercel-labs/skills --skill find-skills

# Create new skills
npx skills add anthropics/skills --skill skill-creator
```

## Current Skills

<!-- Add here each skill you use with: npx skills add <owner/repo> --skill <name> -->

### Base Skills (All SecOps Engineers)

| Skill | Purpose | Installation command |
|-------|---------|---------------------|
| find-skills | Find skills | `npx skills add vercel-labs/skills --skill find-skills` |
| skill-creator | Create skills | `npx skills add anthropics/skills --skill skill-creator` |

### Security and Documentation Skills 🔴 High Priority

| Skill | Purpose | Installation command |
|-------|---------|---------------------|
| doc-coauthoring | Security policies, audit reports, vulnerability assessments, incident reports | `npx skills add anthropics/skills --skill doc-coauthoring` |
| xlsx | Vulnerability tracking, security metrics, compliance checklists, risk matrices | `npx skills add anthropics/skills --skill xlsx` |

### Communication and Reporting Skills 🟡 Medium Priority

| Skill | Purpose | Installation command |
|-------|---------|---------------------|
| internal-comms | Security incident communications, audit findings, compliance updates | `npx skills add anthropics/skills --skill internal-comms` |
| technical-blog-writing | Security best practices, DevSecOps guidelines, security awareness content | `npx skills add 1nference-sh/skills --skill technical-blog-writing` |

## Rule: Add Used Skills

**Every time you use a new skill, add it to the "Current Skills" table.**

Examples of skills to search for:
- `npx skills find security`
- `npx skills find devsecops`
- `npx skills find vulnerability`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/javalenciacai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
