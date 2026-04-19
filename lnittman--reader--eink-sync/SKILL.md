---
name: eink-sync
description: Sync epub library to Xteink X4, manage device content. Triggers: 'sync to x4', 'sync library', 'check x4', 'what's on my reader', 'prepare library for sync'. Use when this capability is needed.
metadata:
  author: lnittman
---

# eink-sync

Sync blog posts and books to Xteink X4 e-reader via WiFi or SD card.

## content policy

The X4 is for **reading** — blog posts, essays, and books only.

| ✓ allowed | ✗ not allowed |
|-----------|---------------|
| Blog articles (RSS, URL, archive harvests) | Agent docs / digests |
| Books (Standard Ebooks, Gutenberg, imports) | Bulk gist dumps |
| Gists explicitly tagged `x4` in notes | Internal docs / rules |

## device folders

```
/
├── blogs/           # articles organized by author/date
│   └── {Author}/{YYYY}/{MM}/{title}.epub
├── books/           # public domain + imports by author
│   └── {Author}/{title}.epub
└── gists/           # only explicitly tagged gists
    └── {Author}/{title}.epub
```

## workflows

### sync to device

```bash
# 1. check device
reader device status --ip <x4-ip>

# 2. sync unsynced items
reader device sync --ip <x4-ip>

# 3. verify
reader device tree --ip <x4-ip> --depth 2
```

### populate library first

```bash
# harvest blog archives
reader blog harvest craig
reader blog harvest thesephist
reader blog harvest macwright

# sync RSS feeds
reader feed sync

# search public domain books
reader search "kafka" --source=standard-ebooks --download

# sync tagged gists (requires notes tag)
reader gist sync --tag x4
```

### device cleanup

```bash
reader device stats --ip <x4-ip>
reader device tree --ip <x4-ip>
reader device clean /old-folder --ip <x4-ip> --force
```

## storage

- Library: `~/.reader/` (DB: `library.db`, files: `library/`)
- SD card: auto-detected by `.crosspoint` marker dir
- WiFi: CrossPoint webserver on port 80/81

## anti-patterns

| don't | do instead |
|-------|-----------|
| `reader gist sync` (no flags) | `reader gist sync --tag x4` |
| sync without checking device | `reader device status` first |
| deep folder nesting (>3) | keep to 2-3 levels |
| large epubs (>5MB) | split or simplify |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lnittman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
