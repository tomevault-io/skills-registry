---
name: ralph
description: | Use when this capability is needed.
metadata:
  author: ribeec20
---

# Ralph Loop Wizard

Interactive wizard to configure and launch a Ralph autonomous agent loop.

## Workflow

Follow these steps sequentially. Each step uses `AskUserQuestion` to collect input from the user.

### Step 1: Discover Providers

Call the `ralph_providers` MCP tool to get:
- Available providers with their models and favorites
- Default provider, model, and iterations
- Summarizer configuration

Store the full response for use in subsequent questions.

### Step 2: Ask Job Type

Use `AskUserQuestion`:
- **Question**: "What type of ralph loop do you want to run?"
- **Header**: "Job type"
- **Options**:
  - **Plan** — "Analyze the codebase and create an implementation plan"
  - **Build** — "Implement features based on an existing plan"

### Step 3: Ask Provider

Use `AskUserQuestion`:
- **Question**: "Which provider should run the loop?"
- **Header**: "Provider"
- **Options**: Build from `ralph_providers` response — use each provider's `display_name` as label, `name` as value. Mark the default provider with "(Default)" in the label.

### Step 4: Ask Iterations

Use `AskUserQuestion`:
- **Question**: "How many loop iterations?"
- **Header**: "Iterations"
- **Options**:
  - **1** — "Single pass"
  - **2** — "Two iterations"
  - **3** — "Three iterations (Recommended)"

### Step 5: Ask Model

Use `AskUserQuestion`:
- **Question**: "Which model should be used?"
- **Header**: "Model"
- **Options**: Show up to 3 favorites for the selected provider from the `ralph_providers` response `favorites` array. If a model is a favorite, include a star in the label (e.g., "sonnet"). The user can always pick "Other" to type a custom model name.
  - If the provider has no favorites, show up to 3 models from the provider's model list instead.

### Step 6: Ask Task Description

Use `AskUserQuestion`:
- **Question**: "What should the loop accomplish? Describe the task."
- **Header**: "Task"
- **Options**:
  - **From clipboard** — "I'll paste the task description"
  - **Write now** — "Let me type it out"

After the user provides their task description text, proceed to launch.

### Step 7: Launch

Derive a slug from the task description (lowercase, hyphens, max 30 chars).

Call the `ralph_start` MCP tool with:
- `slug`: derived slug
- `mode`: "plan" or "build" (from Step 2)
- `provider`: provider name (from Step 3)
- `model`: model name (from Step 5)
- `iterations`: iteration count (from Step 4)
- `task_description`: full task text (from Step 6)

### Step 8: Confirm

Report the job ID and status to the user. Mention they can:
- Check status with `ralph_status`
- Stop the job with `ralph_stop`
- Get a summary with `ralph_summary`
- View progress on the Vista dashboard

## MCP Tools Reference

| Tool | Purpose |
|------|---------|
| `ralph_providers` | Discover available providers, models, favorites, and defaults |
| `ralph_start` | Launch a new ralph loop job |
| `ralph_status` | Check job progress |
| `ralph_stop` | Stop a running job |
| `ralph_summary` | Get AI-generated summary of job artifacts |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ribeec20) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
