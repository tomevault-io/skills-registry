---
name: prompt-engineer
description: Optimize prompts for AI models (Claude, GLM, Nano Banana). Use when asked to optimize a prompt, improve a prompt, write a prompt for, or help with prompt engineering. Use when this capability is needed.
metadata:
  author: bazhand
---

# Prompt Engineer

Optimize prompts for AI models using model-specific best practices.

## Workflow Overview

This skill uses **dynamic questioning** — ask only what's needed based on the user's initial request.

1. **Analyze** the user's request to identify what's known vs. unknown
2. **Ask 1-4 targeted questions** using AskUserQuestion (skip what's already clear)
3. **Read** the appropriate reference file based on model selection
4. **Generate** the optimized prompt (output only the prompt, no commentary)

---

## Phase 1: Analyze the Request

Parse the user's initial prompt request to identify:

### Model Detection
| User mentions | Model identified |
|--------------|------------------|
| "claude", "anthropic", "sonnet", "opus", "haiku" | Claude |
| "glm", "zhipu", "智谱" | GLM (English output) |
| "nano banana", "image", "photo", "picture", "illustration" | Nano Banana |
| None of the above | Unknown → must ask |

**IMPORTANT: All prompts must be optimized for English/US output unless user explicitly specifies otherwise.**

### Content Type Detection
| User mentions | Type identified |
|--------------|-----------------|
| "image", "photo", "portrait", "scene", "illustration" | Image/visual |
| "code", "function", "api", "script", "technical" | Code/technical |
| "story", "article", "copy", "content", "blog" | Creative writing |
| "analyze", "evaluate", "summarize", "research" | Analysis |
| None of the above | Unknown → may ask |

### Requirements Detection
Look for explicit mentions of:
- Style/tone: "professional", "casual", "dramatic", "minimal"
- Format: "JSON", "markdown", "bullet points"
- Constraints: "avoid", "don't include", "no text", "NEVER"

---

## Phase 2: Generate Dynamic Questions

### Rules
1. **1-4 questions maximum** — never overwhelm
2. **Skip what's clear** — don't ask if user already specified
3. **Model is highest priority** — ask first if unknown
4. **2-4 options per question** — user can always select "Other"
5. **Headers max 12 characters**
6. **multiSelect only for "Avoid" questions**

### Decision Matrix

| What's unknown | Question to ask |
|----------------|-----------------|
| Model | Model Selection (required) |
| Content type AND model is image-related | Skip (assume image) |
| Content type AND model is NOT image-related | Content Type |
| Style/requirements AND task is creative/complex | Style/Format |
| Constraints AND model is Nano Banana | Avoidances |

### Question Templates

**Model Selection** (ask if model not detected)
```json
{
  "question": "Which AI model is this prompt for?",
  "header": "Model",
  "multiSelect": false,
  "options": [
    {"label": "Claude", "description": "Anthropic's Claude. Best for reasoning, analysis, code, nuanced tasks."},
    {"label": "GLM", "description": "Zhipu AI's GLM. Optimized for Chinese and structured tasks."},
    {"label": "Nano Banana", "description": "Google's image generation. Photorealistic or artistic images."},
    {"label": "General", "description": "Universal best practices for any LLM."}
  ]
}
```

**Content Type** (ask if ambiguous and not image model)
```json
{
  "question": "What are you creating?",
  "header": "Type",
  "multiSelect": false,
  "options": [
    {"label": "Code or technical", "description": "Functions, APIs, scripts, documentation."},
    {"label": "Creative writing", "description": "Stories, marketing copy, articles."},
    {"label": "Analysis", "description": "Break down problems, evaluate, summarize."},
    {"label": "Other", "description": "I'll describe what I need."}
  ]
}
```

**Image Style** (ask if Nano Banana detected/selected)
```json
{
  "question": "What visual style?",
  "header": "Style",
  "multiSelect": false,
  "options": [
    {"label": "Photorealistic", "description": "Professional photography, camera specs, realistic lighting."},
    {"label": "Artistic", "description": "Illustration, painting, stylized (Ghibli, pixel art, etc)."},
    {"label": "Portrait", "description": "Focus on person/character with detailed features."},
    {"label": "I'll describe", "description": "I have specific style requirements."}
  ]
}
```

**Avoidances** (ask for Nano Banana or if user implies restrictions)
```json
{
  "question": "What should be avoided?",
  "header": "Avoid",
  "multiSelect": true,
  "options": [
    {"label": "Nothing specific", "description": "No particular exclusions needed."},
    {"label": "No text/labels", "description": "No watermarks, text overlays, captions in image."},
    {"label": "No certain elements", "description": "I'll specify what to exclude."},
    {"label": "Keep it simple", "description": "Avoid complexity, jargon, over-elaboration."}
  ]
}
```

---

## Phase 3: Read Reference File

After receiving answers, read ONE reference based on model:

| Model | Reference File |
|-------|---------------|
| Claude | [references/claude-prompting.md](references/claude-prompting.md) |
| GLM | [references/glm-prompting.md](references/glm-prompting.md) |
| Nano Banana | [references/nano-banana-prompting.md](references/nano-banana-prompting.md) |
| General | [references/general-best-practices.md](references/general-best-practices.md) |

---

## Phase 4: Generate the Optimized Prompt

Combine:
- User's original request
- Answers to dynamic questions
- Techniques from the reference file

### Output Format (CRITICAL)

```
[THE COMPLETE OPTIMIZED PROMPT - JUST THE PROMPT, NOTHING ELSE]
```

**DO NOT include:**
- "Here's the optimized prompt:"
- "Applied techniques:" sections
- "Usage tips" or explanations
- Any meta-commentary

The user will paste this directly into the target AI.

---

## Model-Specific Quick Reference

### Claude
- XML tags: `<context>`, `<task>`, `<constraints>`, `<output>`
- Role: "You are a [expert] with [experience]..."
- CoT: "Think step-by-step" + `<thinking>` and `<answer>` tags

### GLM
- Structured headers: `## Task`, `## Requirements`, `## Output Format`
- Numbered steps for sequential tasks
- English output (US locale) unless user specifies otherwise

### Nano Banana
- **ALL CAPS** for emphasis: MUST, NEVER, EXACTLY
- Hex colors: #RRGGBB format
- Camera specs: "Canon EOS R5 with 85mm f/1.4L"
- Quality boost: "Pulitzer Prize winning"
- Negative constraints: "NEVER include text, watermarks..."

### General
- Clear task statement
- Explicit constraints (positive and negative)
- Output format specification
- Examples for pattern-based tasks

---

## Example Scenarios

### Scenario 1: Model specified, type clear
**User:** "optimize a prompt for nano banana cat portrait"

**Analysis:**
- Model: Nano Banana (detected)
- Type: Image/portrait (detected)
- Style: Unknown
- Avoid: Unknown

**Questions to ask:** 2 (Style, Avoid)

### Scenario 2: Nothing specified
**User:** "help me write a better prompt"

**Analysis:**
- Model: Unknown
- Type: Unknown
- Style: Unknown
- Avoid: Unknown

**Questions to ask:** 3 (Model, Type, Format)

### Scenario 3: Model and type specified
**User:** "claude prompt for code review"

**Analysis:**
- Model: Claude (detected)
- Type: Code (detected)
- Style: Unknown (but code review is standard)
- Avoid: N/A

**Questions to ask:** 1 (Format preference)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bazhand) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
