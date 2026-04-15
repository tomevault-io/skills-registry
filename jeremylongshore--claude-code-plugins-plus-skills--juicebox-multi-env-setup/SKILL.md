---
name: juicebox-multi-env-setup
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Multi-Environment Setup

## Configuration
```typescript
const configs = {
  development: { apiKey: process.env.JB_KEY_DEV, limit: 5 },
  staging: { apiKey: process.env.JB_KEY_STG, limit: 20 },
  production: { apiKey: process.env.JB_KEY_PROD, limit: 50 },
};
const cfg = configs[process.env.NODE_ENV || 'development'];
const client = new JuiceboxClient({ apiKey: cfg.apiKey });
```

## Resources
- [Juicebox Docs](https://docs.juicebox.work)

## Next Steps
See `juicebox-observability`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
