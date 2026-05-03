---
name: gemini
description: Quick consultation with Google Gemini AI. Use when you want another AI's perspective on code, architecture, or any technical question. Use when this capability is needed.
metadata:
  author: schmug
---

# Gemini AI Consultation

Query Google Gemini for a second opinion. Pass your question as $ARGUMENTS.

## Usage

```
/gemini What's the best way to handle optimistic updates in React Query?
/gemini Review this authentication flow for security issues
```

## Workflow

### Step 1: Model Selection

Before running the query, check which model is configured. Read `~/.gemini/settings.json` to find the current model setting. If no model is configured there, ask the user which Gemini model to use via AskUserQuestion (provide the latest frontier options you're aware of, plus an "Other" escape hatch). Pass the chosen model with `-m MODEL_NAME`.

If the user has used this skill before in the same session and already selected a model, reuse that choice without asking again.

### Step 2: Context Gathering

If $ARGUMENTS references specific files or code, read those files first and include relevant snippets in the prompt.

### Step 3: Run Query

```bash
gemini -m MODEL_NAME "YOUR PROMPT HERE" 2>/dev/null
```

### Step 4: Present Results

Present Gemini's response to the user. Add your own perspective — note where you agree, disagree, or have additional context.

## Rules

- Always suppress stderr (`2>/dev/null`) to avoid loading/progress noise
- For large context (file contents), pipe via stdin: `echo "CONTEXT" | gemini -m MODEL "QUESTION" 2>/dev/null`
- Keep prompts focused — don't dump entire files unless necessary
- If Gemini returns an error or empty response, inform the user and suggest they check `gemini` CLI auth
- Do NOT use `--yolo` or auto-approval flags — this is read-only consultation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/schmug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
