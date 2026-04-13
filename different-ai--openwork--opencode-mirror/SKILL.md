---
name: opencode-mirror
description: Maintain the local OpenCode mirror for self-reference Use when this capability is needed.
metadata:
  author: different-ai
---

## Quick Usage (Already Configured)

### Update mirror
```bash
git -C vendor/opencode pull --ff-only
```

## Common Gotchas

- Keep the mirror gitignored; never commit `vendor/opencode`.
- Use `--ff-only` to avoid merge commits in the mirror.

## First-Time Setup (If Not Configured)

### Clone mirror
```bash
git clone https://github.com/anomalyco/opencode vendor/opencode
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
