---
name: ote-copy-api
description: Generate On Time Edge copy by calling the local ote_copy_bot API running on localhost:8000. Use when this capability is needed.
metadata:
  author: dillonote
---

Steps:
1) If the API is not running, instruct the user to run:
   uvicorn main:app --reload --port 8000
   and background it with Ctrl+B.
2) Ask for: asset_type, audience, outcome, benefits, capabilities, proof_points, objections, offer_details, CTA.
3) Call the API via curl to /generate and return:
   - the `content` object first
   - then `warnings` (especially banned terms like "IED-Net" and invented stats)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dillonote) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
