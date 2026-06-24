---
name: release-plugin
description: Release a new version of ExpBottleBoost. Use when bumping version, creating releases, or publishing to GitHub/Hangar. Use when this capability is needed.
metadata:
  author: sudolifeagain
---

# Release Plugin

## Quick Release

1. Update version in `pom.xml`
2. Commit changes
3. Create and push tag:
```bash
git tag v1.x.x
git push origin v1.x.x
```

GitHub Actions will automatically:
- Build JAR
- Create GitHub Release
- Publish to Hangar

## Version Update Locations

| File | Field |
|------|-------|
| `pom.xml` | `<version>` |

## Requires (first-time setup)

- `HANGAR_API_TOKEN` in GitHub Secrets
- Hangar API key with `create_version` permission

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sudolifeagain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
