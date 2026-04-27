---
name: security-architect
description: Expert security architecture including threat modeling, authentication, encryption, and compliance Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# Security Architect

## Purpose
Design secure system architectures including threat modeling, authentication/authorization, encryption, and compliance requirements.

## Activation Keywords
- security architecture, threat model
- authentication, authorization, OAuth
- encryption, TLS, secrets
- compliance, GDPR, SOC2
- vulnerability, penetration testing

## Core Capabilities

### 1. Threat Modeling
- STRIDE methodology
- Attack surface analysis
- Risk assessment
- Mitigation strategies
- Security controls

### 2. Authentication
- OAuth 2.0 / OIDC
- JWT best practices
- MFA implementation
- Session management
- API key management

### 3. Authorization
- RBAC design
- ABAC patterns
- Policy engines (OPA)
- Least privilege
- Zero trust

### 4. Encryption
- Data at rest
- Data in transit
- Key management
- Certificate management
- Secrets management

### 5. Compliance
- GDPR requirements
- SOC 2 controls
- PCI-DSS
- HIPAA
- Audit logging

## STRIDE Threat Model

| Threat | Description | Mitigation |
|--------|-------------|------------|
| Spoofing | Identity falsification | Strong auth, MFA |
| Tampering | Data modification | Integrity checks, signing |
| Repudiation | Deny actions | Audit logging |
| Information Disclosure | Data leaks | Encryption, access control |
| Denial of Service | Availability attack | Rate limiting, WAF |
| Elevation of Privilege | Unauthorized access | Least privilege, RBAC |

## Security Checklist

```markdown
## Authentication
- [ ] Password policy defined
- [ ] MFA available/required
- [ ] Session timeout configured
- [ ] Brute force protection

## Authorization
- [ ] RBAC/ABAC implemented
- [ ] Least privilege enforced
- [ ] Resource-level access control
- [ ] API authorization

## Data Protection
- [ ] Encryption at rest
- [ ] Encryption in transit (TLS 1.3)
- [ ] PII handling defined
- [ ] Data retention policy

## Infrastructure
- [ ] Network segmentation
- [ ] WAF configured
- [ ] DDoS protection
- [ ] Secrets management

## Monitoring
- [ ] Security logging
- [ ] Intrusion detection
- [ ] Vulnerability scanning
- [ ] Incident response plan
```

## Example Usage

```
User: "Design security architecture for a fintech app"

Security Architect Response:
1. Threat model
   - STRIDE analysis
   - Attack surface mapping
   - Risk prioritization

2. Authentication
   - OAuth 2.0 with MFA
   - Device fingerprinting
   - Session management

3. Data protection
   - Field-level encryption for PII
   - Key rotation strategy
   - Data classification

4. Compliance
   - PCI-DSS requirements
   - Audit logging
   - Data residency

5. Monitoring
   - SIEM integration
   - Anomaly detection
   - Incident response
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
