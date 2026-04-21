---
name: gemini-cli-model
description: Instructions for selecting and configuring Gemini 3 Pro Preview model in Gemini CLI. Use when the user asks about switching models in Gemini CLI, enabling Gemini 3, or configuring Gemini CLI model settings. Use when this capability is needed.
metadata:
  author: stephenwinters81
---

# Gemini CLI Model Selection

## Selecting Gemini 3 Pro Preview

### Method 1: Interactive Selection (Recommended)

```bash
# First enable preview features
/settings
# Toggle "Preview features" to true

# Then select the model
/model
# Choose "Auto (Gemini 3)" or "Pro"
```

### Method 2: Direct Model Identifier

Use this model ID in config or commands:
```
gemini-3-pro-preview-11-2025
```

### Method 3: Command Line Flag

```bash
gemini --model gemini-3-pro-preview-11-2025
```

## Requirements

- Gemini CLI version 0.21.1 or later
- Paid tier access:
  - Google AI Pro/Ultra subscription
  - Paid API key through Google AI or Vertex
  - Gemini Code Assist with preview models enabled by cloud admin

## Model Routing Options

| Option | Behavior                                                                              |
|--------|---------------------------------------------------------------------------------------|
| Auto   | Intelligently routes - uses Gemini 3 Pro for complex tasks, 2.5 Flash for simple ones |
| Pro    | Forces use of the most capable model (Gemini 3 Pro when enabled)                      |

## Upgrading Gemini CLI

```bash
npm update -g @anthropic-ai/gemini-cli
```

## Verification

After enabling, run `/model` to confirm Gemini 3 Pro Preview is selected.

## References

- https://geminicli.com/docs/get-started/gemini-3/
- https://geminicli.com/docs/cli/model/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stephenwinters81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
