---
name: openevidence-upgrade-migration
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Upgrade & Migration

## Check Version
```bash
npm list | grep openevidence
pip show openevidence 2>/dev/null
```

## Upgrade
```bash
git checkout -b upgrade/openevidence
npm update  # or pip install --upgrade
npm test
```

## Rollback
```bash
git checkout main -- package.json
npm install
```

## Resources
- [OpenEvidence Changelog](https://www.openevidence.com)

## Next Steps
See `openevidence-ci-integration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
