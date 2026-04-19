---
name: pdf-processing
description: Extract text and tables from PDFs; use when PDFs, forms, or document extraction are mentioned. Use when this capability is needed.
metadata:
  author: ba-calderonmorales
---

# PDF Processing
- Use pdfplumber to extract text.
- Install pdfplumber with `pip install pdfplumber`.
- Extract text per page:
  ```python
  import pdfplumber

  with pdfplumber.open("input.pdf") as pdf:
      text = "\n".join(page.extract_text() or "" for page in pdf.pages)
  ```
- For form filling, pair with your form template or validation steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ba-calderonmorales) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
