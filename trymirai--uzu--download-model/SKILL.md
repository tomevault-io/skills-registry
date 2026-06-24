---
name: download-model
description: Download model to test inference engine Use when this capability is needed.
metadata:
  author: trymirai
---

Using ./tools/helpers Python package to download model

## Instructions

1. Go to helpers folder:
   ```
   cd ./tools/helpers
   ```

2. Run:
   ```
   uv run main.py download-model {REPO_ID}
   ```

3. Downloaded model will be in ./models/{ENGINE_VERSION}/{MODEL_NAME} folder

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/trymirai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
