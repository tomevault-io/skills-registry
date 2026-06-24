---
name: juicebox-ci-integration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox CI Integration

## GitHub Actions
```yaml
name: Juicebox Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
  integration:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run test:integration
        env:
          JUICEBOX_API_KEY: ${{ secrets.JUICEBOX_API_KEY }}
```

## Resources
- [Juicebox Docs](https://docs.juicebox.work)

## Next Steps
See `juicebox-deploy-integration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
