---
name: openevidence-ci-integration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence CI Integration

## GitHub Actions
```yaml
name: OpenEvidence Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-deploy-integration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
