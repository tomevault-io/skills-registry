---
name: logics-duplicate-detector
description: Detect potential duplicates across Logics documents. Use when Codex should scan titles and content of requests/backlog/tasks/specs/product briefs/architecture docs to find similar items and propose merge, split, or de-duplication. Use when this capability is needed.
metadata:
  author: alexago83
---

# Duplicate detection

## Run

```bash
python logics/skills/logics-duplicate-detector/scripts/find_duplicates.py --min-score 0.55
```

## Output

- Prints candidate pairs with a similarity score and file paths.
- Use the report to decide: merge docs, close one, or split responsibilities.

## Notes

- Best-effort scoring (title + light content similarity), not a truth oracle.
- By default it ignores docs sharing the same slug across stages (often `request↔backlog↔task` or companion chains); use `--include-related` to include them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
