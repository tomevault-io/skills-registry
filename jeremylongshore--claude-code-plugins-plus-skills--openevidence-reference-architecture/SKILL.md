---
name: openevidence-reference-architecture
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Reference Architecture

## Architecture
```
Client → API Gateway → OpenEvidence Service → OpenEvidence API
                                ↓
                         Data Store → Analytics
```

## Components
```typescript
class OpenEvidenceService {
  private client: any;
  constructor() { this.client = getClient(); }
  // Core business logic wrapping OpenEvidence API
}
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-multi-env-setup`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
