---
name: google-docs-sheets
description: Export Google Docs and Google Sheets (spreadsheets) to Markdown files Use when this capability is needed.
metadata:
  author: mir
---
# Google Docs & Sheets

Export Google Docs and Google Sheets content as Markdown. Uses Google APIs with read-only scopes, prefers gcloud ADC, and falls back to browser OAuth when needed.

## Quick Start

### Auth (preferred: gcloud ADC, run by default)
```bash
gcloud auth application-default login --scopes=https://www.googleapis.com/auth/drive.readonly,https://www.googleapis.com/auth/spreadsheets.readonly
```

## Google Docs

```bash
# Export to stdout
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py docs export <DOC_ID_OR_URL> --stdout

# Export to files (default ./exports when --stdout is not set)
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py docs export <DOC_ID_OR_URL>

# Write to a specific directory
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py docs export <DOC_ID_OR_URL> --output-dir ./exports

# Write and print
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py docs export <DOC_ID_OR_URL> --output-dir ./exports --stdout
```

Notes:
- Export uses HTML -> Markdown conversion and strips images.
- Output filename defaults to the Doc title (sanitized) with `.md` extension.

## Google Sheets

```bash
# Export all tabs to stdout
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py sheets export <SHEET_ID_OR_URL> --stdout

# Export all tabs to files (default ./exports when --stdout is not set)
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py sheets export <SHEET_ID_OR_URL>

# Export specific tabs
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py sheets export <SHEET_ID_OR_URL> --tab "Summary" --tab "Data"

# Header control
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py sheets export <SHEET_ID_OR_URL> --header-row 2
uv run --directory ${AGENTSKILLS_DIR}/skill/google-docs-sheets scripts/cli.py sheets export <SHEET_ID_OR_URL> --no-header
```

Notes:
- Each tab is exported to its own Markdown table.
- Output filenames are `Spreadsheet Title - Tab Title.md`.
- If the URL includes `gid=...`, that tab is selected automatically (unless `--tab` is used).

---
> Source: [mir/maratai](https://github.com/mir/maratai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-05 -->
