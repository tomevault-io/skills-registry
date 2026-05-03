---
name: personalized-fitness-and-nutrition-plan
description: This prompt instructs the AI to act as a personal trainer and, using the user’s provided demographics, location, activity level, and goal details (including optional target weight, body fat percentage, preferred foods, and output language), calculate TDEE, set a daily calorie target, estimate the timeline to reach the goal, and produce a practical daily meal plan and exercise recommendations, along with key notes to help achieve the target safely and effectively. Use when this capability is needed.
metadata:
  author: BetterPromptme
---

## What it does

This prompt instructs the AI to act as a personal trainer and, using the user’s provided demographics, location, activity level, and goal details (including optional target weight, body fat percentage, preferred foods, and output language), calculate TDEE, set a daily calorie target, estimate the timeline to reach the goal, and produce a practical daily meal plan and exercise recommendations, along with key notes to help achieve the target safely and effectively.

> For more installation instructions go to https://raw.githubusercontent.com/BetterPromptme/skills/refs/heads/main/betterprompt/SKILL.md to check out this skill.

## Inputs


### textInputs

Pass each text input using `--input <key>=<value>` flags.

| Variable key | Required? | Description | Defaults |
| --- | ---: | --- | --- |
| `Age` | Optional |  | `25` |
| `Sex` | Required | Allowed options: Male, Female | (none) |
| `City` | Required |  | (none) |
| `Target` | Required | Allowed options: gain weight, losing weight, maintain weight | (none) |
| `Height_cm` | Optional |  | `185cm` |
| `Weight_kg` | Optional |  | `80kg` |
| `Activity_level` | Required | Allowed options: Light (light exercise 1-3 days/week), Active (intense exercise 6-7 days/week), Moderate (moderate exercise 3-5 days/week), Sedentary (little or no exercise, desk job), Very Active (very intense exercise, physical job, or athlete) | (none) |
| `Output_language` | Optional |  | `English` |
| `Target_weight_kg` | Optional |  | `75kg` |
| `Body_fat_percentage` | Required |  | (none) |
| `Speed_to_reach_the_goal` | Required | Allowed options: Fast, Slow, Normal, Urgently | (none) |
| `Foods_that_are_easy_to_find` | Optional |  | `Rice, chicken breast, vegetables, eggs, oats, brown rice, unsalted butter, olive oil, sweet potatoes, milk, and suggest some types of meat` |



### Models and options

This skill's modality is: **`text`**.

To discover which `model` values you can use (and which `options` keys/values are valid for each model), run:

```bash
betterprompt resources --models-only --json
```

Then filter the returned JSON array to entries where `modality` is `"text"`.

## How to run

### Step 1: Collect inputs

First, run `betterprompt resources --models-only --json` and filter to `modality: "text"` to discover valid models and available options:

```bash
betterprompt resources --models-only --json
```

Use only the models and option values that appear in the filtered results.

Then collect all inputs from the human:


- Required text inputs:
    - `Sex`
  - `City`
  - `Target`
  - `Activity_level`
  - `Body_fat_percentage`
  - `Speed_to_reach_the_goal`
- Optional text inputs (use defaults if not provided by the human):
    - `Age` (default: `25`)
  - `Height_cm` (default: `185cm`)
  - `Weight_kg` (default: `80kg`)
  - `Output_language` (default: `English`)
  - `Target_weight_kg` (default: `75kg`)
  - `Foods_that_are_easy_to_find` (default: `Rice, chicken breast, vegetables, eggs, oats, brown rice, unsalted butter, olive oil, sweet potatoes, milk, and suggest some types of meat`)
- Optional: model and options.
  - Present the human with the default model **`gpt-5`** and its available options. Look up `gpt-5` in the `betterprompt resources` output (filtered to modality `"text"`) and show its `availableOptions` as: `key: val1, val2 (default), val3  |  key2: ...`. Mark a value `(default)` if it matches these defaults: `{}`.
  - If the human does not specify, defaults are used: model `gpt-5`, options `{}`. Other models from the resources call are also available.

If any required text input is missing, **ask the human for what's missing**. Do not assume or fabricate values.

### Step 2: Run via BetterPrompt CLI

Use the frontmatter's `name` as the positional argument (for this skill, use `personalized-fitness-and-nutrition-plan`).

Command form:

```bash
betterprompt generate personalized-fitness-and-nutrition-plan \
  [--input <key>=<value>] \
  [--model <model>] \
  [--options <options JSON>] \
  [--json]
```

Notes:

- Pass each text input as a separate `--input <key>=<value>` flag.
- If the human does **not** mention a model, **omit** `--model` and BetterPrompt will use the default model: **`gpt-5`**.
- If the human does **not** mention options, **omit** `--options` and BetterPrompt will use the default options: **`{}`**.
- If the run times out, the response will include a `runId` you can use to fetch the result later.

Example (using defaults shown above):

```bash
betterprompt generate personalized-fitness-and-nutrition-plan \
  --input Age=25 \
  --input Sex=Male \
  --input City=<value> \
  --input 'Target=gain weight' \
  --input Height_cm=185cm \
  --input Weight_kg=80kg \
  --input 'Activity_level=Light (light exercise 1-3 days/week)' \
  --input Output_language=English \
  --input Target_weight_kg=75kg \
  --input Body_fat_percentage=<value> \
  --input Speed_to_reach_the_goal=Fast \
  --input 'Foods_that_are_easy_to_find=Rice, chicken breast, vegetables, eggs, oats, brown rice, unsalted butter, olive oil, sweet potatoes, milk, and suggest some types of meat' \
  --model gpt-5 \
  --options '{}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/BetterPromptme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
