---
name: tracker-codebase-codestyle
description: Rules to follow and guidelines for working on this codebase. Use when this capability is needed.
metadata:
  author: eighttrigrams
---

- We use HoneySQL instead of bashing SQL strings together
- Try to keep the frontend "as dumb as possible", i.e. when asked to implement filtering of items, make sure that is done "behind the API" i.e. in the backend (instead of delivering *all* items to the frontend and filtering there)
- Scheduling logic (weekly/biweekly/monthly date calculation) is shared: backend in `et.tr.scheduling`, frontend in `et.tr.ui.scheduling`. Both meeting series and recurring tasks use these shared modules.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eighttrigrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
