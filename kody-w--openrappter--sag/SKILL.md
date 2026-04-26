---
name: sag
description: Search and Grep - powerful code search across repositories and file systems. Use when this capability is needed.
metadata:
  author: kody-w
---

# SAG (Search and Grep)

Fast code search using ripgrep.

## Basic Search

```bash
rg "pattern" /path/to/search
```

## Search by File Type

```bash
rg "function" --type ts
rg "class" --type py
```

## Search with Context

```bash
rg "TODO" -C 3
```

## Search and Replace

```bash
rg "old_name" --files-with-matches | xargs sed -i '' 's/old_name/new_name/g'
```

## Useful Flags

- `-i` — case insensitive
- `-w` — whole word match
- `-l` — files only
- `-c` — count matches
- `--hidden` — include hidden files
- `--glob '!node_modules'` — exclude patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
