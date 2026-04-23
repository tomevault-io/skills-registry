---
name: security-audit
description: Enforces CSP, input sanitization, encryption standards, and GDPR compliance for clinical data Use when this capability is needed.
metadata:
  author: do-ops885
---

## What I do

I enforce security standards across the application. I validate Content Security Policy (CSP), input sanitization, encryption implementations, and GDPR compliance for handling patient health information.

## When to use me

Use this when:

- Adding new dependencies or changing architecture
- You need to validate security compliance
- You're implementing new data handling features

## Key Concepts

- **CSP**: Content Security Policy for XSS prevention
- **Input Sanitization**: Clean all user inputs
- **Encryption**: AES-256-GCM for data at rest
- **GDPR**: Data privacy compliance for EU users
- **HIPAA**: Health data protection (US)

## Source Files

- `services/crypto.ts`: Encryption implementation
- `plans/04_security_audit.md`: Security audit plan
- `index.html`: CSP configuration

## Code Patterns

- Validate CSP headers in index.html
- Check all crypto operations use approved algorithms
- Ensure no sensitive data in logs or errors

## Operational Constraints

- All patient data must be encrypted (AES-256-GCM)
- No sensitive data in logs or console output
- GDPR consent for data processing
- Audit all data access and modifications

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/do-ops885) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
