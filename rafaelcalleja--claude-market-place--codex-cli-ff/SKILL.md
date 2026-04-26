---
name: codex-cli-ff
description: This skill should be used when the user asks to "run codex autonomously", "fire-and-forget codex", "codex-cli-ff", "execute codex in background", or wants to run OpenAI Codex CLI without interactive prompts. Autonomous Codex execution with aggressive defaults via forked subagent. Use when this capability is needed.
metadata:
  author: rafaelcalleja
---

# Codex CLI Fire-and-Forget

## Environment Check
- Codex version: !`codex --version 2>/dev/null || echo "CODEX_NOT_INSTALLED"`

## Task

Execute the following Codex CLI task autonomously with complete output capture.

### Argument Parsing

Parse `$ARGUMENTS` to extract optional flags and the task description:

**Optional flags** (override defaults if present):
- `--model <MODEL>` — Override default model (see `references/models.md` for available models)
- `--effort <LEVEL>` — Override reasoning effort (low, medium, high, xhigh)
- `--sandbox <MODE>` — Override sandbox mode (read-only, workspace-write, danger-full-access)

**Defaults** (used when flags are not specified):
- Model: `gpt-5.3-codex`
- Effort: `xhigh`
- Sandbox: `workspace-write`
- Auto: `--full-auto` (always enabled)

**Everything after the flags is the task description.**

### Command Assembly

Assemble the codex command following this pattern:

```bash
codex exec \
  -m <model> \
  --config model_reasoning_effort="<effort>" \
  --sandbox <sandbox> \
  --full-auto \
  "<task description>"
```

### Execution

1. If the environment check above shows `CODEX_NOT_INSTALLED`, stop immediately and report the error
2. Assemble the command from parsed arguments and defaults
3. Execute the command via Bash
4. Capture and return the **complete** stdout and stderr — do not summarize or truncate

### Input

```
$ARGUMENTS
```

## Additional Resources

For model catalog, sandbox modes, and CLI flag reference, consult:
- **`references/models.md`** — Complete model table, effort levels, sandbox modes, and command examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rafaelcalleja) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
