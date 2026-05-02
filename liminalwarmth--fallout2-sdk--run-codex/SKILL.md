---
name: run-codex
description: > Use when this capability is needed.
metadata:
  author: liminalwarmth
---

# Codex CLI

Send `$ARGUMENTS` as a prompt to Codex via `codex exec`. The entire argument string is the prompt — no mode parsing needed.

## How to run

```bash
cd "$(git rev-parse --show-toplevel)"
OUTFILE=$(mktemp /tmp/codex_result.XXXXXX)
/opt/homebrew/bin/codex exec "$ARGUMENTS" \
  -m gpt-5.3-codex \
  -c model_reasoning_effort="high" \
  --ephemeral --full-auto \
  -o "$OUTFILE" 2>&1
```

Read `$OUTFILE` for the output. If the exit code is non-zero, read `$OUTFILE` for the error.

## After running

1. Present Codex's output under a **Codex** heading
2. Add your own take under a **Claude** heading
3. Note agreements, disagreements (with resolution), and blind spots
4. List concrete next steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liminalwarmth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
