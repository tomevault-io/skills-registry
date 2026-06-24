---
name: security-review
description: Perform a security architecture review with Zero Trust assessment - identifies authentication/authorization gaps, data protection issues, and provides remediation guidance Use when this capability is needed.
metadata:
  author: melodic-software
---

# Security Review Command

This command performs comprehensive security architecture reviews with Zero Trust assessment.

## Purpose

Generate security assessments including:

1. Zero Trust alignment evaluation
2. Authentication/authorization gap analysis
3. Data protection review
4. Network security assessment
5. Prioritized remediation guidance

## Workflow

### Phase 1: Scope Definition

Clarify the review scope:

**If a scope is provided:**

- Search the codebase for relevant components
- Identify authentication mechanisms
- Locate authorization policies
- Find encryption configurations
- Check for secrets management

**If no scope provided, ask:**

```text
What would you like me to review?
1. Entire system architecture
2. API layer security
3. Service-to-service communication
4. Data storage and protection
5. Specific component/service
```

### Phase 2: System Discovery

Understand the security landscape:

```text
Security Discovery Checklist:
□ Authentication mechanisms (OAuth, OIDC, JWT, API keys)
□ Authorization model (RBAC, ABAC, ACLs)
□ Encryption at rest (databases, files, secrets)
□ Encryption in transit (TLS, mTLS)
□ Secrets management (Vault, cloud KMS, env vars)
□ Network boundaries and segmentation
□ Logging and audit trails
□ Third-party integrations
```

**Search patterns:**

- Authentication: `auth`, `login`, `jwt`, `oauth`, `oidc`, `bearer`
- Authorization: `roles`, `permissions`, `authorize`, `policy`, `claims`
- Encryption: `encrypt`, `decrypt`, `kms`, `key`, `certificate`, `tls`
- Secrets: `secret`, `credential`, `password`, `apikey`, `connection`

### Phase 3: Zero Trust Assessment

Evaluate against Zero Trust principles:

#### 1. Verify Explicitly

```text
Assessment: Verify Explicitly

Questions:
□ Is every request authenticated, regardless of source?
□ Are all API endpoints protected (including internal)?
□ Is token validation performed on every request?
□ Are sessions properly validated and can be revoked?

Findings:
- [ ] External APIs authenticated: [YES/NO/PARTIAL]
- [ ] Internal APIs authenticated: [YES/NO/PARTIAL]
- [ ] Token validation: [Strong/Weak/None]
- [ ] Session management: [Proper/Needs work]
```

#### 2. Least Privilege

```text
Assessment: Least Privilege

Questions:
□ Do service accounts have minimal required permissions?
□ Are user permissions scoped appropriately?
□ Is privilege escalation prevented?
□ Are administrative functions separated?

Findings:
- [ ] Service account scoping: [Tight/Loose/Overprivileged]
- [ ] User permission model: [Granular/Coarse/None]
- [ ] Admin separation: [Implemented/Missing]
```

#### 3. Assume Breach

```text
Assessment: Assume Breach

Questions:
□ Is network traffic encrypted even internally?
□ Are secrets rotatable without downtime?
□ Is blast radius limited (microsegmentation)?
□ Are anomalies detectable (monitoring, SIEM)?

Findings:
- [ ] Internal encryption: [mTLS/TLS/None]
- [ ] Secret rotation capability: [Automated/Manual/None]
- [ ] Segmentation: [Microsegmented/Basic/Flat]
- [ ] Detection capability: [Advanced/Basic/None]
```

### Phase 4: Authentication & Authorization Review

Detailed analysis:

**Authentication:**

```text
Authentication Analysis:

Mechanism: [OAuth 2.0 / OIDC / JWT / Basic / API Key / Other]

Strengths:
- [List positive findings]

Weaknesses:
- [List concerns]

Recommendations:
- [Prioritized improvements]

Token Security:
- Algorithm: [RS256/HS256/Other]
- Expiration: [Duration]
- Refresh handling: [Secure/Concerns]
- Revocation: [Supported/Not supported]
```

**Authorization:**

```text
Authorization Analysis:

Model: [RBAC / ABAC / ACL / Custom]

Implementation:
- Enforcement point: [Gateway/Service/Both]
- Policy storage: [Where defined]
- Policy evaluation: [How decisions made]

Gaps:
- [Identify missing controls]

Recommendations:
- [Prioritized improvements]
```

### Phase 5: Data Protection Review

Analyze data security:

**Encryption:**

```text
Encryption Assessment:

At Rest:
- Database: [AES-256/Other/None] via [mechanism]
- File storage: [Encrypted/Unencrypted]
- Backups: [Encrypted/Unencrypted]

In Transit:
- External: [TLS 1.3/TLS 1.2/Other]
- Internal: [mTLS/TLS/Plaintext]

Key Management:
- Storage: [HSM/KMS/Config/Code]
- Rotation: [Automated/Manual/None]
- Access control: [Strict/Loose]
```

**Secrets Management:**

```text
Secrets Assessment:

Storage Location:
- [ ] Dedicated secrets manager (Vault, AWS SM, Azure KV)
- [ ] Environment variables
- [ ] Configuration files
- [ ] Hardcoded (CRITICAL)

Rotation Capability:
- Database credentials: [Automatic/Manual/Never]
- API keys: [Automatic/Manual/Never]
- Certificates: [Automatic/Manual/Never]

Access Control:
- Who can access secrets: [Documented/Unknown]
- Audit logging: [Enabled/Disabled]
```

### Phase 6: Network Security Review

Analyze network protection:

```text
Network Security Assessment:

Segmentation:
- Network topology: [Microsegmented/Segmented/Flat]
- Service mesh: [Yes - Type / No]
- Trust boundaries: [Defined/Unclear]

Traffic Control:
- Ingress: [WAF/API Gateway/Direct]
- Egress: [Controlled/Open]
- East-West: [Controlled/Open]

Service Communication:
- Protocol: [gRPC+mTLS/HTTPS/HTTP]
- Service identity: [SPIFFE/Certificates/None]
- Policy enforcement: [Service mesh/Manual/None]
```

### Phase 7: OWASP API Security Assessment

Check against OWASP API Security Top 10:

```text
OWASP API Security Top 10 Assessment:

1. Broken Object Level Authorization
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

2. Broken Authentication
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

3. Broken Object Property Level Authorization
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

4. Unrestricted Resource Consumption
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

5. Broken Function Level Authorization
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

6. Unrestricted Access to Sensitive Business Flows
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

7. Server Side Request Forgery
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

8. Security Misconfiguration
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

9. Improper Inventory Management
   Status: [PASS/FAIL/NEEDS REVIEW]
   Notes: [Findings]

10. Unsafe Consumption of APIs
    Status: [PASS/FAIL/NEEDS REVIEW]
    Notes: [Findings]
```

### Phase 8: Generate Report

Create the security assessment report:

```text
# Security Assessment Report: [System/Component]

## Executive Summary

Risk Level: [CRITICAL/HIGH/MEDIUM/LOW]
Key Findings: [X] critical, [Y] high, [Z] medium

## Zero Trust Assessment

| Principle | Status | Score |
|-----------|--------|-------|
| Verify explicitly | [PASS/PARTIAL/FAIL] | X/10 |
| Least privilege | [PASS/PARTIAL/FAIL] | X/10 |
| Assume breach | [PASS/PARTIAL/FAIL] | X/10 |

Overall Zero Trust Maturity: [X/30]

## Findings

### [CRITICAL] Finding Title
**Category**: [Auth/Data/Network/Config]
**Component**: [Affected component]
**Risk**: [Impact description]
**Recommendation**: [How to fix]
**Priority**: Immediate

[Repeat for all findings by severity]

## Remediation Roadmap

### Immediate (0-7 days)
- [ ] [Critical items]

### Short-term (1-4 weeks)
- [ ] [High priority items]

### Medium-term (1-3 months)
- [ ] [Medium priority items]

### Long-term (3-6 months)
- [ ] [Hardening items]
```

## Usage Examples

```bash
# Review entire system
/sd:security-review

# Review specific component
/sd:security-review payment-service

# Review with architecture context
/sd:security-review @docs/architecture.md

# Review API layer only
/sd:security-review "API gateway and endpoints"
```

## Interactive Elements

Use `AskUserQuestion` to:

- Clarify review scope
- Understand compliance requirements (SOC2, HIPAA, PCI)
- Validate risk assessments
- Prioritize findings
- Confirm remediation approach

## Output

The command produces:

1. **Executive Summary** - Risk level and key findings
2. **Zero Trust Assessment** - Alignment with ZT principles
3. **Detailed Findings** - Categorized by severity
4. **OWASP Assessment** - API security checklist results
5. **Remediation Roadmap** - Prioritized action items

## Related Skills

This command leverages:

- `zero-trust-architecture` - Zero Trust principles and patterns
- `api-security` - API authentication and authorization
- `mtls-service-mesh` - Service mesh security
- `secrets-management` - Secrets handling best practices

## Related Agent

For ongoing security consultation:

- `security-reviewer` - Security architecture expertise

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
