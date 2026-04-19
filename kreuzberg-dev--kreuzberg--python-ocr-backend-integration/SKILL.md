---
name: python-ocr-backend-integration
description: python ocr uackend integration Use when this capability is needed.
metadata:
  author: kreuzberg-dev
---

Integrate EasyOCR and PaddleOCR via Python

1. Wrap Python OCR class
2. Cache name and supported languages
3. For process_image():
   a. Clone Python object reference
   b. Spawn blocking task
   c. Acquire GIL inside task
   d. Call Python method
   e. Translate exceptions to Rust errors
   f. Return structured result
4. Handle timeouts and cancellation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kreuzberg-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
