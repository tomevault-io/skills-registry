---
name: gemini
description: Interact with Google's Gemini model via CLI. Use when needing a second opinion from another LLM, cross-validation, or leveraging Gemini's Google Search grounding. Supports multi-turn conversations with session management. Use when this capability is needed.
metadata:
  author: junoh-moon
---

# Gemini CLI

## Commands

Use the wrapper script for streamlined interaction:

```bash
# New conversation
~/.agents/skills/gemini/gemini-chat.sh "your prompt here"

# Continue conversation (session_id from previous output)
~/.agents/skills/gemini/gemini-chat.sh --resume <session_id> "follow-up prompt"
```

The script:
- Streams output in real-time (no more waiting blindly)
- Shows retry/error info (429 등)
- Saves raw response to tmpfile for debugging
- Extracts session_id automatically

## Examples

```bash
# Ask Gemini for code review
~/.agents/skills/gemini/gemini-chat.sh "Review this function for potential bugs: $(cat src/utils.ts)"

# Follow up (use session_id from previous output)
~/.agents/skills/gemini/gemini-chat.sh --resume abc-123 "How would you refactor it?"

# Leverage Google Search grounding
~/.agents/skills/gemini/gemini-chat.sh "What are the latest changes in TypeScript 5.8?"
```

## Raw CLI Usage (if needed)

```bash
tmpfile=$(mktemp)
echo "Response saved to: $tmpfile"
gemini --model auto-gemini-3 --approval-mode yolo "prompt" --output-format stream-json | tee "$tmpfile"
session_id=$(jq -rs 'last | .session_id' "$tmpfile")
```

## Model Selection

Available `--model` options:

| Value | Description |
|-------|-------------|
| `auto-gemini-3` | **기본값**. gemini-3-pro, gemini-3-flash 중 자동 선택 |
| `auto-gemini-2.5` | gemini-2.5-pro, gemini-2.5-flash 중 자동 선택 |
| `gemini-2.5-flash` | 직접 지정 (가장 안정적) |
| `gemini-2.5-pro` | 직접 지정 |

### Quota Exhausted (429 에러) 대처

Gemini 3 quota 초과 시 (429, `MODEL_CAPACITY_EXHAUSTED`, `RESOURCE_EXHAUSTED`):

```bash
# auto-gemini-2.5로 fallback (2.5-pro 또는 2.5-flash 자동 선택)
~/.agents/skills/gemini/gemini-chat.sh --model auto-gemini-2.5 "your prompt"
```

**Fallback 순서:**
1. `auto-gemini-3` (기본) → Gemini 3 계열 사용
2. `auto-gemini-2.5` (fallback) → Gemini 2.5-pro/flash 중 자동 선택

## Notes

- Gemini automatically uses `google_web_search` when needed
- Image analysis: include file path in prompt, Gemini reads via `read_file`
- Raw response file remains after execution for debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/junoh-moon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
