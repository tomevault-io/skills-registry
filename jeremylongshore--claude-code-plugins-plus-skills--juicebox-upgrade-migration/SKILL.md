---
name: juicebox-upgrade-migration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Upgrade & Migration

## Check Version
```bash
npm list @juicebox/sdk
npm view @juicebox/sdk version
```

## Upgrade
```bash
git checkout -b upgrade/juicebox-sdk
npm install @juicebox/sdk@latest
npm test
```

## Rollback
```bash
npm install @juicebox/sdk@previous-version --save-exact
```

## Resources
- [Changelog](https://docs.juicebox.work/changelog)

## Next Steps
See `juicebox-ci-integration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
