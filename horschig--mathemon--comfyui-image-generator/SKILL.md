---
name: comfyui-image-generator
description: Design and generation workflow for new project assets (portraits, icons, arena art). Use when the implementation requires a visual resource that does not yet exist in `assets/`. Use when this capability is needed.
metadata:
  author: horschig
---

# ComfyUI Image Generator

Engineer high-fidelity art assets consistent with the "Brutal" game aesthetic.

## Workflow

1. **Style Lookup**: Inspect current textures in `assets/textures/` to align lighting and grit.
2. **Prompt Engineering**: Use the [prompts.md](references/prompts.md) patterns to construct species-aligned tokens.
3. **Workflow Selection**:
   - Load `text_to_image_standard.json`.
   - Configure seed and target aspect ratio.
4. **Validation**: Check output against naming conventions (e.g., `por_orc_warrior.png`).

## Guidelines
- **Consistency**: Use the same base model (SDXL) for all core species.
- **Batching**: Generate 4-8 variations before finalizing a choice.

## Artefacts to Update (new assets)

- When adding new assets, place them under `assets/textures/` and update any `.tres` resources that reference them.
- Add a line to `./docs/todo/master_todo.md` indicating the new assets and which scenes or resources will use them.
- Commit assets in a separate commit and include small preview images in the PR description for faster review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
