---
name: comfyui-image-variant
description: Transformation workflow for creating asset derivatives (state changes, damage levels, UI status effects) from existing base images. Use when a resource already exists but needs a variant. Use when this capability is needed.
metadata:
  author: horschig
---

# ComfyUI Image Variant

Produce stylistic or state-based derivatives while maintaining structural parity.

## Workflow

1. **Delta Definition**: Determine the target change (e.g., "damaged version," "alternative expression").
2. **Technique Selection**:
   - Consult [variant-config.md](references/variant-config.md) for Denoising and ControlNet settings.
   - Use **Inpainting** for localized changes (e.g., changing just a helmet).
3. **Execution**:
   - Load `image_to_image_controlled.json`.
   - Pass the base image as the initial latent.
4. **Integration**: Update `.tres` Resource files to include the new variant path.

## Guidelines
- **Parity**: Compare the original and variant at 50% opacity to ensure no "drift" in character anatomy.
- **Naming**: Use logical suffixes (e.g., `_inj`, `_up1`, `_stat_stun`).

## Artefacts to Update (when applicable)

- If the new variant introduces or changes resource paths, update the corresponding `.tres` files and list the change in `./docs/todo/master_todo.md` under the related story.
- If prompts, skills, or docs refer to the asset names or behavior, add a short note in those files listing the updated paths (e.g., `./.github/prompts/`, `./.github/skills/`, `./docs/`).
 - Commit artefact changes separately with a clear message (e.g., `enh - doc: add image variant <name>`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
