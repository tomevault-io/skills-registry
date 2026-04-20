---
name: image-editing
description: Edit, modify, or enhance existing images using natural language instructions. Use this skill when the user wants to change colors, remove backgrounds, add objects, or modify the style of an image they have uploaded or generated. Use when this capability is needed.
metadata:
  author: tara-shopos
---

# Image Editing Skill

## When to use this skill
Use this skill when the user provides an image (or refers to a previous image) and asks to modify it. Common intents include:
- "Make the background blue"
- "Remove the background"
- "Add a shadow"
- "Change the shoe to red"
- "Make it look cinematic"

## Instructions
1.  **Identify the image**: Ensure you have the URL or ID of the image to be edited. If not provided, ask the user to upload or select one.
2.  **Analyze the request**: Determine the specific editing operation (mask-based edit, global style transfer, background removal).
3.  **Construct the tool call**:
    - Call the `editImage` tool.
    - `prompt`: A clear description of the desired output (e.g., "a red shoe on a white background").
    - `image`: The source image data or URL.
    - `mask_description`: If editing a specific part, describe what to mask (e.g., "the shoe").

## Examples

### Input
> User: "Change the background to a sunny beach" (attached image of a bottle)

### Execution
- **Tool**: `editImage`
- **Arguments**:
  - `prompt`: "bottle on a sunny beach"
  - `image`: [Image Data]
  - `editType`: "background-replacement"

---

### Input
> User: "Make it a sketch"

### Execution
- **Tool**: `editImage`
- **Arguments**:
  - `prompt`: "sketch of the bottle"
  - `editType`: "style-transfer"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tara-shopos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
