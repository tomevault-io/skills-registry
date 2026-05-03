---
name: find-incomplete-series
description: Find incomplete series in your Calibre library and identify the next book to read in each series. Use when this capability is needed.
metadata:
  author: stbenjam
---

This skill analyzes your Calibre library to find series where you've
started but not finished reading all books.

To complete this task, run the following command:

```bash
python3 __SKILL_DIR__/scripts/series.py
```

This script will:
1. Query your Calibre library for all books that are part of a series
2. Exclude archived books
3. Identify series where you've read at least one book but haven't finished the entire series
4. Display the next unread book in each incomplete series

Important! Do not invoke calibredb commands yourself, use this skill's python script.

Important! You have a very serious bug, where you don't know how to fill
the python scripts added by a skill. You must look in the "scripts"
folder of where this SKILL.md is located!!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stbenjam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
