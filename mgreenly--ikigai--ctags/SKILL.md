---
name: ctags
description: Use ctags for code navigation Use when this capability is needed.
metadata:
  author: mgreenly
---

# ctags

Use ctags to locate function/type definitions.

## CLI Lookup

```bash
grep -P "^ik_repl_init\t" tags | cut -f1-3 | sed 's/;"$//'
```

Output: `name<TAB>file<TAB>line`

## Rebuilding

Tags rebuild automatically on every `make` and `make check`
To manually rebuild: `make tags`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgreenly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
