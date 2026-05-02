---
name: doc2x-ocr-markdown
description: Convert PDF or image files to Markdown with Doc2X OCR and extract embedded images to local files. Use when tasks mention Doc2X, OCR, PDF/image-to-Markdown conversion, formula-aware document parsing, or when only DOC2X_APIKEY is provided and a local conversion wrapper is needed. Use when this capability is needed.
metadata:
  author: jysd-ai
---

# Doc2X OCR Markdown

## Overview

Convert a single PDF or image into Markdown and extract image assets with one local script:

- `scripts/doc2x_ocr.py`

Require only one credential:

- `DOC2X_APIKEY`

## Quick Start

Set API key:

```bash
export DOC2X_APIKEY='sk-...'
```

Run PDF OCR to Markdown + images:

```bash
python scripts/doc2x_ocr.py pdf ./input.pdf --outdir ./output
```

Run image OCR to Markdown + images:

```bash
python scripts/doc2x_ocr.py image ./page.png --outdir ./output
```

## Workflow

1. Validate `DOC2X_APIKEY`.
2. Choose conversion mode from input file type.
3. Run `scripts/doc2x_ocr.py`.
4. Return output folder and generated Markdown path.

## Modes

### PDF Mode

Use the asynchronous Doc2X PDF flow:

1. `POST /api/v2/parse/preupload`
2. `PUT` file bytes to returned upload URL
3. Poll `GET /api/v2/parse/status`
4. Trigger export `POST /api/v2/convert/parse` (`to=md`)
5. Poll `GET /api/v2/convert/parse/result`
6. Download zip, extract files, locate Markdown

Useful options:

- `--formula-mode dollar|normal` (default `dollar`)
- `--merge-cross-page-forms`
- `--poll-interval`
- `--timeout`
- `--keep-zip`

### Image Mode

Use synchronous image layout OCR:

1. `POST /api/v2/parse/img/layout` with binary image body
2. Write page Markdown from response
3. If `convert_zip` exists, decode and extract image resources

## Output Contract

For input `<name>.pdf` or `<name>.png`, script writes:

- `<outdir>/<name>/...` extracted files
- `<outdir>/<name>/<name>.md` if no Markdown file exists in extracted content

Script prints a JSON summary with:

- `mode`
- `uid`
- `output_dir`
- `markdown`
- `zip` (only when `--keep-zip`)

## References

Read these files when you need deeper context:

- `references/api-quick-reference.md` for endpoint behavior and limits
- `references/implementation-notes.md` for relation to the copied official `doc2x.py`

## Troubleshooting

- Handle `parse_task_limit_exceeded` or `parse_concurrency_limit` by reducing concurrent jobs and retrying later.
- Split huge PDFs if parse timeout or page-limit errors occur.
- Keep poll interval between 1 and 3 seconds for status APIs unless there is a strong reason to change.
- Save outputs promptly because official docs state cloud parse results are temporary.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jysd-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
