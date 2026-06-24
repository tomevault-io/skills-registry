---
name: pst-converter
description: Convert Microsoft Outlook Personal Storage Table (.pst) files into AI-friendly formats (Markdown, JSON, CSV). Use this skill to extract emails, metadata, and attachments from legacy email archives for analysis, context, or migration. Use when this capability is needed.
metadata:
  author: nweii
---

# PST Converter Skill

## Description

This skill provides a tool to extract and format data from Outlook PST files. It is useful for:

- Reading legacy email archives.
- Converting PST content to Markdown for LLM context.
- Extracting metadata for analysis (CSV/JSON).

## Requirements

- **System**: `libpst` must be installed.
  - macOS: `brew install libpst`
  - Linux: `sudo apt-get install pst-utils`
- **Python**: Python 3.8+ (Standard library only).

## Instructions

Run the `cli.py` script found in this directory.

### Basic Syntax

```bash
python3 cli.py <path_to_pst_file> [options]
```

### Options

- `-f, --format`: Output format. Choices: `markdown` (default), `json`, `csv`, `both` (md+json), `all`.
- `--full`: Include full email bodies (default is metadata-only).
- `--folders`: Preserve original folder structure.
- `--include-threading`: Include thread ID metadata.
- `-o, --output <dir>`: Specify output directory (default: `./output`).

## Examples

### 1. Quick Summary (Metadata only)

Good for scanning an archive's contents.

```bash
python3 cli.py archive.pst
```

### 2. Prepare for LLM Context (Markdown)

Extracts full content into a readable Markdown format.

```bash
python3 cli.py archive.pst --full -f markdown
```

### 3. Data Analysis (JSON/CSV)

Extract structured data.

```bash
python3 cli.py archive.pst --full -f json
```

## Troubleshooting

- **`readpst` not found**: Install `libpst` via your system package manager.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nweii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
