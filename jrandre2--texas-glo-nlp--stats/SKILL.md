---
name: stats
description: Show project database statistics including document counts, entity counts, Harvey funding totals, and spatial coverage. Use when this capability is needed.
metadata:
  author: jrandre2
---

# Show Project Statistics

Run the project statistics commands and present a clear summary.

1. Run `make stats` in the project root to get database snapshot counts
2. If that fails, run the individual stat commands:
   - `python src/pdf_processor.py --stats`
   - `python src/nlp_processor.py --stats`
   - `python src/financial_parser.py --stats`
3. Present the results in a clear, organized format highlighting:
   - Total documents, pages, and tables processed
   - Entity counts by type
   - Harvey funding totals and activity counts
   - Latest quarter available

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jrandre2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
