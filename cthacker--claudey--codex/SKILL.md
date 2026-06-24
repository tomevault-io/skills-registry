---
name: codex
description: Delegate a task to OpenAI Codex for an independent analysis. Use when asked to get a second opinion, run Codex, use Codex for review, or when a task would benefit from a separate model's perspective — code review, security audit, plan review, architecture analysis, etc. Use when this capability is needed.
metadata:
  author: cthacker
---

# Codex

Run a task through OpenAI's Codex CLI in read-only mode and report the results back.

## Step 1: Determine the prompt

- If the user provided a specific task (e.g., `/codex review this PR for security issues`), use that as the Codex prompt.
- If the user invoked `/codex` without arguments, ask what they want Codex to analyze.
- Prepend context to the prompt as needed — current branch, relevant file paths, or a summary of recent changes — so Codex has enough information to produce useful output.

## Step 2: Run Codex

Execute the following command via Bash, substituting `<PROMPT>` with the full prompt from Step 1:

```bash
codex exec --sandbox read-only -m gpt-5.3-codex -c model_reasoning_effort=high "<PROMPT>" 2>/dev/null
```

**Notes:**
- Stderr is redirected to `/dev/null` because Codex streams thinking tokens there, which would consume excessive context.
- If the command fails (auth error, model unavailable), tell the user and suggest checking that `CODEX_API_KEY` or `OPENAI_API_KEY` is set.
- For large codebases, set a generous timeout (up to 10 minutes).

## Step 3: Capture and save output

1. Capture the stdout from the Codex command — this is the final analysis.
2. Write it to a markdown file in the project root: `codex-report-YYYY-MM-DD-HHMMSS.md`, with a brief header noting the prompt used and model.
3. Summarize the key findings inline in the conversation so the user gets an immediate overview without opening the file.

## Output format

The inline summary should be concise — highlight the top findings, flag anything critical, and note where the full report is saved. Example:

> **Codex analysis complete** — saved to `codex-report-2026-02-07-143022.md`
>
> Key findings:
> - [finding 1]
> - [finding 2]
> - [finding 3]
>
> See the full report for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cthacker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
