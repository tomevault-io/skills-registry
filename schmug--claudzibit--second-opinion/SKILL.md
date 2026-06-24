---
name: second-opinion
description: Get multiple AI perspectives (Gemini + Codex) on a question. Use for architectural decisions, security reviews, or when you want validation from other AIs. Use when this capability is needed.
metadata:
  author: schmug
---

# Second Opinion — Multi-AI Consultation

Query both Gemini and Codex with the same question to compare perspectives. Pass your question as $ARGUMENTS, optionally followed by file paths for context.

## Usage

```
/second-opinion What's the best error handling strategy for this API?
/second-opinion Is this auth flow secure? server/auth.ts
/second-opinion Should we use WebSockets or SSE here? server/realtime.ts client/src/hooks/useStream.ts
```

## Workflow

### Step 1: Model Selection

Check configured models for both CLIs:
- Read `~/.gemini/settings.json` for the Gemini model
- Read `~/.codex/config.toml` for the Codex model

If either is missing, ask the user which frontier model to use for that CLI via AskUserQuestion. Reuse prior session choices if available.

### Step 2: Context Gathering

Parse $ARGUMENTS — the question is the text, any paths at the end are context files. If file paths are provided, read them and prepare a context block (limit to ~200 lines per file, truncate if needed).

Format the prompt:
```
Question: [the question]

Context:
--- [filename] ---
[file contents]
---
```

### Step 3: Query Both AIs

Query Gemini:
```bash
echo "FORMATTED_PROMPT" | gemini -m GEMINI_MODEL "Answer the question above based on the provided context" 2>/dev/null
```

Query Codex:
```bash
codex exec -m CODEX_MODEL "FORMATTED_PROMPT" -o /tmp/codex-second-opinion.txt 2>/dev/null; cat /tmp/codex-second-opinion.txt 2>/dev/null; rm -f /tmp/codex-second-opinion.txt
```

### Step 4: Present Comparative Results

```
## Gemini's Take (model: X)
[response summary]

## Codex's Take (model: Y)
[response summary]

## My Synthesis
[where they agree, where they differ, and your own recommendation]
```

## Rules

- Query both AIs even if one fails — present whatever you get
- If a CLI errors out, note it and continue with the other
- Keep context focused — don't include entire large files, extract the relevant sections
- Always add your own synthesis — don't just relay responses
- Do NOT use auto-approval flags on either CLI
- Clean up any temp files

---
> Source: [schmug/claudzibit](https://github.com/schmug/claudzibit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
