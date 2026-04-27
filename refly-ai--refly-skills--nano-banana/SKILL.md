---
name: nano-banana
description: Generate AI images using Nano Banana for fast image creation. Use when you need to: (1) quickly generate images from prompts, (2) create prototypes and iterations, or (3) produce simple image content efficiently. Use when this capability is needed.
metadata:
  author: refly-ai
---

# Nano Banana

Generate AI images using Nano Banana for fast image creation. Use when you need to: (1) quickly generate images from prompts, (2) create prototypes and iterations, or (3) produce simple image content efficiently.

## Input

Provide input as JSON:

```json
{
  "image_prompt": "Text description of the image you want to generate (e.g., 'a serene mountain landscape at sunset')",
  "image_style": "Optional style modifier (e.g., 'photorealistic', 'anime style', 'oil painting')"
}
```

## Execution (Pattern A: File Generation)

### Step 1: Run the Skill and Get Run ID

```bash
RESULT=$(refly skill run --id skpi-c3kbyakremol9ukap585h4xd --input '{
  "image_prompt": "a serene mountain landscape at sunset with snow peaks",
  "image_style": "photorealistic"
}')
RUN_ID=$(echo "$RESULT" | jq -r '.payload.workflowExecutions[0].id')
# RUN_ID is we-xxx format, use this for workflow commands
```

### Step 2: Open Workflow in Browser and Wait for Completion

```bash
open "https://refly.ai/workflow/c-fi0jawxn5au4gc4u34x642ko"
refly workflow status "$RUN_ID" --watch --interval 30000
```

### Step 3: Download and Show Result

```bash
# Get files from this run
FILES=$(refly workflow toolcalls "$RUN_ID" --files --latest | jq -r '.payload.files[]')

# Download and open each file
echo "$FILES" | jq -c '.' | while read -r file; do
  FILE_ID=$(echo "$file" | jq -r '.fileId')
  FILE_NAME=$(echo "$file" | jq -r '.name')
  if [ -n "$FILE_ID" ] && [ "$FILE_ID" != "null" ]; then
    refly file download "$FILE_ID" -o "$HOME/Desktop/${FILE_NAME}"
    open "$HOME/Desktop/${FILE_NAME}"
  fi
done
```

## Expected Output

- **Type**: Image
- **Format**: .png/.jpg image file
- **Location**: `~/Desktop/`
- **Action**: Opens automatically to show user

## Rules

Follow base skill workflow: `~/.claude/skills/refly/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/refly-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
