---
name: triton-hip-reference-kernel-search
description: Search and adapt Triton/HIP kernel patterns from a corpus to optimize AMD GPUs; use to find similar ops and reuse tiling/occupancy strategies. Use when this capability is needed.
metadata:
  author: AMD-AGI
---

# AMD Kernel Patterns

- Use when you need real kernel templates (attention, layernorm, matmul, activations) to adapt for AMD/ROCm.
- Do not load the entire corpus; grep targeted snippets instead.

## How to use
- Search `references/train_crawl.json` with ripgrep for relevant ops; keep context tight.
- Extract only needed code and descriptions; rewrite for wave64 occupancy, LDS tiling, vectorized/coalesced access, and bank-conflict avoidance.
- Cite source file and lines; pair with reflection prompts to validate correctness and performance.

## References
- `references/SEARCH.md`: Grep commands and tips for slicing snippets efficiently.

---
> Source: [AMD-AGI/Apex](https://github.com/AMD-AGI/Apex) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
