---
name: hle-reasoning-wrapper
description: Wraps HLE benchmark questions in a structured Chain-of-Thought (CoT) reasoning process. Use when answering HLE questions to ensure strict step-by-step logic and format compliance. Use when this capability is needed.
metadata:
  author: openclaw
---

# HLE Reasoning Wrapper

Enforces a structured reasoning process for Humanity's Last Exam (HLE) questions.

## Usage

```javascript
const hle = require('./index');
const prompt = hle.formatPrompt("What is the speed of light?");
// Use prompt with LLM
const result = hle.validateOutput(llmResponse);
```

## Logic

1.  **Format Prompt**: Injects required structure (Thought/Answer).
2.  **Validate Output**: Ensures the model followed the structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
