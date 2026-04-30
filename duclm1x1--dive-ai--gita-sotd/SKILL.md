---
name: gita-sotd
description: > Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Bhagavad Gita Slok of the Day

Fetch verses from the Bhagavad Gita using the free [vedicscriptures API](https://vedicscriptures.github.io/).

## Usage

Run the script to get a slok:

```bash
# Daily slok (deterministic, changes each day)
python3 scripts/fetch_slok.py

# Specific verse
python3 scripts/fetch_slok.py --chapter 2 --verse 47

# Random verse
python3 scripts/fetch_slok.py --random

# Different translator (prabhu, siva, purohit, gambir, chinmay, etc.)
python3 scripts/fetch_slok.py --translator siva

# Raw JSON output
python3 scripts/fetch_slok.py --json
```

## Available Translators

- `prabhu` - A.C. Bhaktivedanta Swami Prabhupada (default)
- `siva` - Swami Sivananda
- `purohit` - Shri Purohit Swami
- `gambir` - Swami Gambirananda
- `chinmay` - Swami Chinmayananda
- `tej` - Swami Tejomayananda (Hindi)
- `rams` - Swami Ramsukhdas (Hindi)
- `raman` - Sri Ramanuja

## Output Format

The script outputs formatted markdown with:

- Chapter and verse reference
- Sanskrit text (optional)
- Transliteration
- English/Hindi translation with author attribution

## API Reference

Base URL: `https://vedicscriptures.github.io`

- `GET /slok/:chapter/:verse` - Get specific verse
- `GET /chapter/:ch` - Get chapter info
- `GET /chapters` - List all chapters

The Bhagavad Gita has 18 chapters with 700 total verses.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
