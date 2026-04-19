---
name: daytona-sdk
description: Use when working with Daytona SDK - uploadFile(Buffer, path), Bun install, CodeLanguage options
metadata:
  author: spences10
---

# Daytona SDK

## Quick Start

```typescript
// uploadFile: (content: Buffer, destination: string)
await sandbox.fs.uploadFile(
	Buffer.from(code),
	'/home/daytona/app.ts',
);
```

## Core Principles

- uploadFile takes `(Buffer, path)` not `(path, content)` - reversed
- CodeLanguage only supports: `python`, `typescript`, `javascript`
- Research via `gh search issues --repo daytonaio/daytona <query>`

## References

- [api-gotchas.md](references/api-gotchas.md) - Common API mistakes
- [snapshots.md](references/snapshots.md) - Pre-built images
- [tier-limits.md](references/tier-limits.md) - Preview vs Tier 3+

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spences10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
