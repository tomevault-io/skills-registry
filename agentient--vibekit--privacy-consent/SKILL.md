---
name: privacy-consent
description: > Use when this capability is needed.
metadata:
  author: agentient
---

# Privacy and Consent

> **STUB: This skill is not yet implemented**
>
> This placeholder preserves the documented plugin structure.
> See parent plugin README for planned capabilities.

## Planned Capabilities

- **GDPR Compliance**: Consent management, data subject rights
- **CCPA Compliance**: California Consumer Privacy Act requirements
- **Data Minimization**: Collect only necessary data
- Consent tracking and audit trails
- Cookie consent implementation
- Privacy policy generation guidance

## Critical Pattern

```typescript
// WRONG - track before consent
analytics.init();
analytics.track('page_view');

// CORRECT - check consent first
if (await userConsent.hasAnalyticsConsent()) {
  analytics.init();
  analytics.track('page_view');
}
```

## Implementation Status

- [ ] Core implementation
- [ ] References documentation
- [ ] Output templates
- [ ] Integration tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
