---
name: document-creator-sophnet
description: Use when working with an integrated document creation skill that supports creating Word documents (DOCX) and PowerPoint presentations (PPTX), with automatic upload to OSS and URL return.
metadata:
  author: openclaw
---
# Document Creator with Auto-Upload SophNet

## Description

An integrated document creation skill that supports creating Word documents (DOCX) and PowerPoint presentations (PPTX), with automatic upload to OSS and URL return.

## Usage

```bash
# Create document using skill
openclaw document-creator /path/to/your/file.txt

# Or call via session
# In OpenClaw session: Create document /path/to/file.png
```

## Configuration Requirements

- Set `SOPH_API_KEY` environment variable
- Or configure sophnet API key in OpenClaw config file

## Supported Parameters

- `file_path`: Local file path to upload (required)

## Return Value

- Success: Returns signed URL string
- Failure: Throws exception with error message

## Dependencies

- Python 3.7+
- requests library
- OpenClaw config access permission

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
