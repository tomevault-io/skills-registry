---
name: fetch-source
description: > Use when this capability is needed.
metadata:
  author: okwasniewski
---

# Fetch Source Code

Use `opensrc` to download and explore package source code.

## Commands

```bash
npx opensrc <package>           # npm package (e.g., npx opensrc zod)
npx opensrc pypi:<package>      # Python package (e.g., npx opensrc pypi:requests)
npx opensrc crates:<package>    # Rust crate (e.g., npx opensrc crates:serde)
npx opensrc <owner>/<repo>      # GitHub repo (e.g., npx opensrc vercel/ai)
```

## Rules

- Always run in `/tmp` to avoid cluttering working directory
- Use when you need to understand library internals, not just API surface

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/okwasniewski) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
