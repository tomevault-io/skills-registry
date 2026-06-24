---
name: install-component-guide
description: Fetches and splits the daisyUI style guide from the official LLM text file into organized sections Use when this capability is needed.
metadata:
  author: noshishirust
---

The daisyUI style guide can be obtained from the official LLM text at https://daisyui.com/llms.txt.

1. **Fetch the style guide**: GET https://daisyui.com/llms.txt and retrieve all content.
2. **Split into sections**: Split the content and organize into the `.daisyui` directory.
   1. When splitting, save the main style guide directly in the `.daisyui` directory, and save component sections in `.daisyui/components`
3. Delete the fetched `llms.txt` file at the end.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noshishirust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
