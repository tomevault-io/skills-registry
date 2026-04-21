---
name: spec-sync
description: Use when behavior or API changes require updating documentation or specs.
metadata:
  author: sanmak
---
# Goal
Keep product docs and API specs consistent with code changes.

# Do
- Update relevant files in `spec/` for product behavior changes.
- Update the root `openapi.yaml` when API shapes, endpoints, or examples change.
- Keep examples aligned with actual request/response payloads.

# Don't
- Leave docs/specs stale after changing behavior.

# Examples
- "Add a new endpoint and update `openapi.yaml`."
- "Change debate flow and update the related spec in `spec/`."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sanmak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
