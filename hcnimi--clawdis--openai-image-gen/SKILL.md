---
name: openai-image-gen
description: Batch-generate images via OpenAI Images API. Random prompt sampler + `index.html` gallery. Use when this capability is needed.
metadata:
  author: hcnimi
---

# OpenAI Image Gen

Generate a handful of “random but structured” prompts and render them via the OpenAI Images API.

## Run

```bash
python3 {baseDir}/scripts/gen.py
open ~/Projects/tmp/openai-image-gen-*/index.html  # if ~/Projects/tmp exists; else ./tmp/...
```

Useful flags:

```bash
python3 {baseDir}/scripts/gen.py --count 16 --model gpt-image-1
python3 {baseDir}/scripts/gen.py --prompt "ultra-detailed studio photo of a lobster astronaut" --count 4
python3 {baseDir}/scripts/gen.py --size 1536x1024 --quality high --out-dir ./out/images
```

## Output

- `*.png` images
- `prompts.json` (prompt → file mapping)
- `index.html` (thumbnail gallery)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hcnimi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
