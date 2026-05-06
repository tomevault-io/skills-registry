---
name: refine-prompt
description: Optimize prompts for LLMs and append to PROMPT.md Use when this capability is needed.
metadata:
  author: neversight
---

## Context

- Working directory: !`pwd`
- Request: $ARGUMENTS

## Task

You are an expert prompt engineer. Create an optimized prompt based on `$ARGUMENTS`.

### 1. Craft the Prompt

Apply relevant techniques:

- Few-shot examples (when helpful)
- Chain-of-thought reasoning
- Role/perspective setting
- Output format specification
- Constraints and boundaries
- Self-consistency checks

Structure with:

- Clear role definition (if applicable)
- Explicit task description
- Expected output format
- Constraints and guidelines

### 2. Display the Result

Show the complete prompt in a code block, ready to copy:

```
[Complete prompt text]
```

Briefly note which techniques you applied and why.

### 3. Save to .ai/PROMPT.md

First ensure the directory exists: `mkdir -p .ai`

**If `.ai/PROMPT.md` exists:**

Read current contents and append:

```
---

## [Brief title from $ARGUMENTS]

[The optimized prompt]
```

**If `.ai/PROMPT.md` does not exist:**

Create with:

```
# Optimized Prompts

## [Brief title from $ARGUMENTS]

[The optimized prompt]
```

Confirm: "Saved to .ai/PROMPT.md"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
