---
name: receipt-parser-engineer
description: Create, fix, and refine deterministic receipt/invoice parsers in curlys-books, including vendor detection, OCR text extraction routing (pdfplumber for text PDFs → AWS Textract fallback; images → Textract), golden fixture creation, and updates to the vendor dispatcher/registry. Use when adding a new vendor parser, debugging mis-detections, improving totals/tax/date/line extraction, or deciding when to rely on Claude Vision fallback for vendors without a tested parser. Use when this capability is needed.
metadata:
  author: thomasmccrossin
---

# Receipt Parser Engineer

## Overview

Build and maintain vendor parsers that turn extracted receipt text into `ReceiptNormalized` with high accuracy, backed by golden fixtures. Keep parsing deterministic where possible and treat Claude Vision as the safety net for unknown vendors.

## Workflow Decision Tree

1. **Start from the file type**
   - **PDF**: use embedded text when possible; if the PDF has little/no embedded text, OCR it.
   - **Image**: OCR it.
   - **Email HTML/text**: parse directly (no OCR).

2. **Decide whether to write/extend a deterministic parser**
   - Write/refine a deterministic parser when the vendor is high-volume, has structured line items, or needs reliable tax/subtotal/total.
   - Prefer Claude Vision fallback when the vendor is low-volume, highly variable, or you lack enough samples to stabilize patterns.

3. **Choose the OCR/text-extraction path**
   - **Rule:** pdfplumber is for text-based PDFs; **AWS Textract** is for images and anything pdfplumber can’t extract meaningfully from a PDF.

## Core Invariants

- Do not introduce any local OCR-binary wrapper or dependency; use Textract for OCR.
- Keep vendor parsers deterministic: parse from `ocr_text` (and `pdf_path` only when table extraction is required).
- Every parser change ships with a golden fixture test that reproduces the bug and prevents regressions.
- Avoid “magic balancing” lines: prefer `validation_warnings` for missing/faded items rather than inventing data.

## Workflow: Add a New Vendor Parser (Deterministic)

1. **Collect samples**
   - Target 3–10 real receipts/invoices with known-good totals.
   - Prefer multiple layouts (thermal vs letter, refunds vs purchases, discounts/deposits).

2. **Generate OCR text for fixtures**
   - Use the OCR factory (pdfplumber → Textract fallback) from the worker container:
     - `docker compose exec worker python scripts/test_vendor_parsers.py /path/to/receipt.pdf`
   - Save to `tests/fixtures/golden_receipts/<vendor>/<name>_ocr.txt`.

3. **Create expected outputs**
   - Copy a known-good parse (or fill by hand) into:
     - `tests/fixtures/golden_receipts/<vendor>/<name>_expected.json`
   - Keep expected JSON minimal: only assert the fields you truly want stable.

4. **Implement the parser**
   - Add `packages/invoice_parsers/vendors/<vendor>_parser.py`:
     - `detect_format(ocr_text) -> bool` should be strict enough to avoid false positives.
     - `parse(ocr_text, entity, pdf_path=...) -> ReceiptNormalized` should be resilient to OCR noise.
   - If table extraction is required, accept `pdf_path` and use `pdfplumber` inside the parser.

5. **Register the parser**
   - Update `packages/invoice_parsers/vendor_dispatcher.py`:
     - import your parser
     - add it to the parser list (before `GenericParser`)
     - add the vendor key to `GOLDEN_VENDORS` when it has golden coverage

6. **Add golden tests**
   - Update `tests/unit/test_invoice_parsers.py` with a new `@pytest.mark.golden` class for the vendor.
   - Assert at minimum: `vendor_guess`, totals, purchase date, invoice number (if applicable), and line count.

7. **Run tests**
   - `make test-golden`
   - If you touch shared parsing behavior, run `make test-unit` too.

## Workflow: Fix/Refine an Existing Parser

1. Add a failing fixture first (new `<name>_ocr.txt` + `<name>_expected.json`).
2. Make the smallest parser change that fixes the issue.
3. Re-run `make test-golden` until green.
4. Add/adjust `validation_warnings` when the receipt can’t be made internally consistent.

## Debugging Playbook

- **One-shot OCR + parse (local workbench):**
  - `python3 skills/receipt-parser-engineer/scripts/parser_workbench.py --file /path/to/receipt.pdf --entity corp`
  - `python3 skills/receipt-parser-engineer/scripts/parser_workbench.py --ocr-text tests/fixtures/golden_receipts/<vendor>/<name>_ocr.txt --entity corp`

- **OCR routing:** `packages/parsers/ocr/factory.py`
- **Textract provider:** `packages/parsers/ocr/provider_textract.py`
- **PDF embedded text extraction:** `packages/parsers/ocr/pdf_text_extractor.py`
- **Dispatcher + golden vendor list:** `packages/invoice_parsers/vendor_dispatcher.py`
- **Worker parse routing (incl. Claude Vision heuristic):** `services/worker/tasks/pipeline/parse.py`
- **Golden fixtures:** `tests/fixtures/golden_receipts/`

## References (load as needed)

- Agent prompt (Claude/Codex): `references/agent-prompt.md`
- Repo/pipeline map: `references/pipeline-map.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomasmccrossin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
