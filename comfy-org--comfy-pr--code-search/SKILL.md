---
name: comfyui-code-search
description: Search code across ComfyUI repositories using comfy-codesearch service. Including all Comfy-Org repos and Community made Custom Node Repos on GitHub. Use when this capability is needed.
metadata:
  author: comfy-org
---

# ComfyUI Code Search

Search for code patterns, functions, and implementations:
prbot code search --query "<search terms>"

NOTE: Does NOT support --limit parameter. Results are automatically paginated.

Examples:
prbot code search --query "binarization" --repo Comfy-Org/ComfyUI
prbot code search --query "authentication function"
prbot code search --query "video transcription whisper"

Best practices:

- Use specific function names or patterns for better results.
- Specify repo when you know which repository to search.
- Review results and cite file paths and line numbers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/comfy-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
