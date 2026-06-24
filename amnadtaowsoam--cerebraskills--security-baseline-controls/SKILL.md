---
name: security-baseline-controls
description: Minimum security controls every service must implement: authentication, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Security Baseline Controls

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Minimum security controls every service must implement: authentication, authorization, input validation, secrets handling, and security headers that make system secure from day one.

## Why This Matters
- **Compliance**: Meet SOC2, ISO27001, GDPR requirements
- **Defense in depth**: Multiple security layers
- **Consistency**: Same security posture across services
- **Audit**: Clear evidence of controls

## Core Concepts & Rules

### 1. Core Principles
- Follow established patterns and conventions
- Maintain consistency across codebase
- Document decisions and trade-offs

### 2. Implementation Guidelines
- Start with the simplest viable solution
- Iterate based on feedback and requirements
- Test thoroughly before deployment


## Inputs / Outputs / Contracts
* **Inputs**:
  - <e.g., env vars, request payload, file paths, schema>
* **Entry Conditions**:
  - <Pre-requisites: e.g., Repo initialized, DB running, specific branch checked out>
* **Outputs**:
  - <e.g., artifacts (PR diff, docs, tests, dashboard JSON)>
* **Artifacts Required (Deliverables)**:
  - <e.g., Code Diff, Unit Tests, Migration Script, API Docs>
* **Acceptance Evidence**:
  - <e.g., Test Report (screenshot/log), Benchmark Result, Security Scan Report>
* **Success Criteria**:
  - <e.g., p95 < 300ms, coverage ≥ 80%>

## Skill Composition
* **Depends on**: [api-style-guide](./api-style-guide/SKILL.md), [secrets-key-management](../71-infrastructure-patterns/secrets-key-management/SKILL.md)
* **Compatible with**: [service-standards-blueprint](./service-standards-blueprint/SKILL.md), [logging-metrics-tracing-standard](./logging-metrics-tracing-standard/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [owasp-top-10](../../24-security-practices/owasp-top-10/SKILL.md), [security-audit](../../24-security-practices/security-audit/SKILL.md)

## Quick Start
#

## Assumptions
- Services need security baseline
- Authentication and authorization required
- Input validation necessary
- Secrets management system available
- Dependency scanning in CI/CD
- Compliance requirements (SOC2, ISO27001, GDPR)

## Compatibility
- **Node.js**: 16+
- **TypeScript**: 4.5+
- **Helmet**: 7.0+
- **JWT**: jsonwebtoken 9.0+
- **Zod**: 3.0+ (for validation)

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Unauthenticated request | Request without auth | 401 Unauthorized | Response status |
| Invalid token | Expired/invalid token | 401 Unauthorized | Token validation |
| Invalid input | Malformed input | 400 Bad Request | Input validation |
| SQL injection | SQL in input | Sanitized/rejected | No SQL injection |
| XSS attempt | Script in input | Escaped/sanitized | No XSS execution |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
Security controls are complete when:

- [ ] Authentication on all endpoints (except health)
- [ ] Authorization checks for protected resources
- [ ] Input validation on all user inputs
- [ ] Security headers configured
- [ ] Secrets from secret manager (not env/code)
- [ ] Dependencies scanned for vulnerabilities
- [ ] Security audit logging enabled
- [ ] CSP configured for web apps
- [ ] HSTS enabled on production
- [ ] Regular security reviews scheduled

## Anti-patterns
1. **Security as afterthought**: Add security later
2. **Trust internal traffic**: No service-to-service auth
3. **Log everything**: Including secrets
4. **Ignore dependencies**: Old vulnerable packages
5. **Over-logging**: Log PII/credentials then say "will delete later" (can't do reliably)

## Reference Links
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [Security Headers](https://securityheaders.com/)
- [Helmet Documentation](https://helmetjs.github.io/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
