---
name: kimi-cli-skill
description: Reference guide for integrating and operating Kimi Code CLI in Ode, with focus on print mode, sessions, and automation-safe defaults. Use when this capability is needed.
metadata:
  author: odefun
---
## What I do
- Provide Kimi CLI integration guidance for Ode agent providers.
- Document reliable non-interactive invocation patterns using `--print`.
- Explain session reuse (`--session`), working directory scoping (`--work-dir`), and JSONL output parsing.
- Call out automation implications like implicit YOLO behavior in print mode.

## When to use me
Use this when adding or debugging the `kimi` provider in Ode, especially command construction, streaming output parsing, or script/CI behavior.
Ask clarifying questions if you need model/auth-specific setup beyond CLI invocation.

## Recommended invocation pattern
- Base command: `kimi --print --output-format stream-json --session <id> --work-dir <cwd> -p <prompt>`
- For final-only text output: `--final-message-only` (or `--quiet` shortcut)
- For structured pipelines: `--input-format=stream-json --output-format=stream-json`

## Integration notes for Ode
- Treat print mode as auto-approved execution (`--yolo` is implicit).
- Parse stdout as JSONL message stream (`assistant` and optional `tool` messages).
- Keep provider model input hidden in UI unless explicitly needed.
- Keep session IDs stable per Slack thread and rotate when environment changes.

## Sources
- https://moonshotai.github.io/kimi-cli/zh/customization/print-mode.html
- https://moonshotai.github.io/kimi-cli/zh/reference/kimi-command.html
- https://moonshotai.github.io/kimi-cli/zh/customization/print-mode.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/odefun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
