---
name: component-catalog
description: How to access metadata about Vivid components. Use to list available components or query their API. Use when this capability is needed.
metadata:
  author: Vonage
---

The API of all components is defined in `libs/components/metadata.json`.
Since the file is very large, always use scripting (e.g. with `jq`) to extract just the data that you need.

The content of the file has the type `Metadata` defined in `libs/metadata-extractor/src/metadata/format.ts`.
Read the TypeScript definition to understand the structure.

---
> Source: [Vonage/vivid-3](https://github.com/Vonage/vivid-3) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
