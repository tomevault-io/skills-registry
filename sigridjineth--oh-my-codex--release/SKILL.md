---
name: release
description: Automated release workflow for oh-my-codex Use when this capability is needed.
metadata:
  author: sigridjineth
---

# Release Skill

Automate the release process for oh-my-codex.

## Usage

```
/oh-my-codex:release <version>
```

Example: `/oh-my-codex:release 2.4.0` or `/oh-my-codex:release patch` or `/oh-my-codex:release minor`

## Release Checklist

Execute these steps in order:

### 1. Version Bump
Update version in all locations:
- `package.json`
- `src/installer/index.ts` (VERSION constant)
- `src/__tests__/installer.test.ts` (expected version)
- `.codex-plugin/plugin.json`
- `README.md` (version badge and title)

### 2. Run Tests
```bash
npm run test:run
```
All 231+ tests must pass before proceeding.

### 3. Commit Version Bump
```bash
git add -A
git commit -m "chore: Bump version to <version>"
```

### 4. Create & Push Tag
```bash
git tag v<version>
git push origin main
git push origin v<version>
```

### 5. Publish to npm
```bash
npm publish --access public
```

### 6. Create GitHub Release
```bash
gh release create v<version> --title "v<version> - <title>" --notes "<release notes>"
```

### 7. Verify
- [ ] npm: https://www.npmjs.com/package/oh-my-codex
- [ ] GitHub: https://github.com/sigridjineth/oh-my-codex/releases

## Version Files Reference

| File | Field/Line |
|------|------------|
| `package.json` | `"version": "X.Y.Z"` |
| `src/installer/index.ts` | `export const VERSION = 'X.Y.Z'` |
| `src/__tests__/installer.test.ts` | `expect(VERSION).toBe('X.Y.Z')` |
| `.codex-plugin/plugin.json` | `"version": "X.Y.Z"` |
| `README.md` | Title + version badge |

## Semantic Versioning

- **patch** (X.Y.Z+1): Bug fixes, minor improvements
- **minor** (X.Y+1.0): New features, backward compatible
- **major** (X+1.0.0): Breaking changes

## Notes

- Always run tests before publishing
- Create release notes summarizing changes
- Plugin marketplace syncs automatically from GitHub releases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sigridjineth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
