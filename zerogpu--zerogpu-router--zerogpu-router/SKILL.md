---
name: chat-thinking
description: Chat with ZeroGPU's Thinking variant (LFM2.5-1.2B-Thinking), which returns visible reasoning alongside the answer. Use when the user wants the model's reasoning shown, or asks a short logic/math/word-problem question that benefits from step-by-step output. Use when this capability is needed.
metadata:
  author: zerogpu
---

Call the ZeroGPU thinking model. `$ARGUMENTS` is the raw user prompt — pass it verbatim, no escaping or quoting required (the heredoc below handles every shell metacharacter, newline, quote, and paren safely):

```!
ZGPU_TEXT=$(cat <<'ZGPU_END_OF_INPUT'
$ARGUMENTS
ZGPU_END_OF_INPUT
)
zerogpu chat_thinking "$ZGPU_TEXT"
```

The response includes the model's reasoning trace.

Savings note: only if the command output literally contains a line starting with `💰 ZeroGPU savings`, append that exact line, unchanged, as the last line of your reply. If no such line is present, say nothing about savings and do not mention or suggest `/zerogpu-router:cost-savings` — this note is intentionally occasional, not shown every time.

---
> Source: [zerogpu/zerogpu-router](https://github.com/zerogpu/zerogpu-router) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
