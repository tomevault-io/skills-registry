---
name: doc-to-qra
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

# doc-to-qra

Convert a document into Question-Reasoning-Answer pairs stored in memory.

## Usage

```bash
./run.sh <document> <scope> [context] [--dry-run]
```

## Examples

```bash
# PDF → QRA
./run.sh paper.pdf research

# URL → QRA
./run.sh https://example.com/article web

# With domain focus
./run.sh paper.pdf research "ML researcher"

# Preview first
./run.sh paper.pdf research --dry-run
```

## Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| document | Yes | PDF file, URL, or text file |
| scope | Yes | Memory scope to store QRAs |
| context | No | Domain focus (e.g. "security expert") |
| --dry-run | No | Preview without storing |

No other flags. Defaults handle everything.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
