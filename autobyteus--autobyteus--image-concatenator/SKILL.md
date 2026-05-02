---
name: image-concatenator
description: Use when working with a utility skill to vertically concatenate multiple images into a single file.
metadata:
  author: autobyteus
---

# Image Concatenator

This skill provides a tool to vertically merge images.

### Capabilities

- **Concatenate**: Merges list of images into one.

### How to use

Use the `run_bash` tool to run the skill’s script.

Provide the output path first, followed by one or more input image paths. Resolve the
script path from the skill root before invoking the tool. The script lives at
`scripts/concatenate_images.py`.

**Example:**
If the user asks to merge `a.png` and `b.png` into `result.png`, run the `run_bash`
tool with `scripts/concatenate_images.py result.png a.png b.png` as the command.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/autobyteus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
