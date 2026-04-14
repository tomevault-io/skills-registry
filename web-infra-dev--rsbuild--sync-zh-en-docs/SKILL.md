---
name: sync-zh-en-docs
description: Sync uncommitted docs between `website/docs/zh` and `website/docs/en`. Use when authors update docs in one language and need to align the mirrored `.md`/`.mdx` file in the other language. Use when this capability is needed.
metadata:
  author: web-infra-dev
---

# Sync Zh/En Documentation

## Steps

1. Check uncommitted changes under `website/docs/zh` and `website/docs/en`.

2. Translate each changed file to the counterpart path (`zh` <-> `en`), and keep:

- meaning and structure consistent
- technical terms / commands / code blocks unchanged unless localization is required
- concise, clear, professional technical-doc style

3. Format and verify:

```bash
node --run format
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/web-infra-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
