---
name: gdpr
description: GDPR compliance including privacy by design, data protection requirements, consent management, right to be forgotten, and data breach response Use when this capability is needed.
metadata:
  author: hack23
---

# GDPR Compliance Skill

## Purpose

Ensures compliance with EU General Data Protection Regulation (GDPR) for systems that process personal data.

## Rules

### Privacy by Design (Article 25)

**MUST:**
- Implement data minimization (collect only necessary data)
- Use pseudonymization where possible
- Encrypt personal data at rest and in transit
- Implement access controls
- Enable data portability
- Design for right to erasure

### Lawful Basis for Processing

**MUST HAVE one of:**
- Consent (freely given, specific, informed, unambiguous)
- Contract (necessary for contract performance)
- Legal obligation
- Vital interests
- Public task
- Legitimate interests

### Data Subject Rights

**MUST SUPPORT:**
- Right to access (provide copy within 30 days)
- Right to rectification (correct inaccurate data)
- Right to erasure ("right to be forgotten")
- Right to restrict processing
- Right to data portability
- Right to object

### Data Breach Response (Article 33-34)

**MUST:**
- Detect breach within reasonable time
- Assess risk to data subjects
- Notify supervisory authority within 72 hours (if high risk)
- Notify affected individuals without undue delay
- Document all breaches

### Hack23 Homepage Context

**No Personal Data Processing:**
- Static website, no user accounts
- No cookies, no tracking
- No forms, no personal data collection
- Privacy by design: collect nothing

**Privacy Policy MUST State:**
- No personal data collected
- No cookies used
- Server logs may contain IP addresses (retained 90 days)
- Contact information for data protection inquiries

## Related Policies

- [Privacy Policy](https://github.com/Hack23/ISMS-PUBLIC/blob/main/Privacy_Policy.md)
- [Data Classification Policy](https://github.com/Hack23/ISMS-PUBLIC/blob/main/Data_Classification_Policy.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
