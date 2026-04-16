---
name: openevidence-multi-env-setup
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Multi-Environment Setup

## Configuration
```typescript
const configs = {
  development: { apiKey: process.env.OPENEVIDENCE_API_KEY_DEV },
  staging: { apiKey: process.env.OPENEVIDENCE_API_KEY_STG },
  production: { apiKey: process.env.OPENEVIDENCE_API_KEY_PROD },
};
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-observability`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
