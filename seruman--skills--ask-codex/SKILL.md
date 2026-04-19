---
name: ask-codex
description: Use OpenAI Codex CLI as a second-opinion AI tool for debugging, code analysis, and complex technical questions. Trigger when stuck on subtle bugs, need a second perspective on code, want to consult another AI model for verification, or when multiple debugging approaches have failed. Also trigger when the user explicitly asks to use Codex, asks for a "second opinion," or wants to cross-check analysis with another model. Use when this capability is needed.
metadata:
  author: seruman
---

# Codex Assistant

Codex is an external AI-powered CLI tool. Use it as a second perspective for difficult debugging, code analysis, or spec-conformance questions.

## When to Use

- Stuck after multiple debugging attempts
- Subtle bugs (bitstream alignment, off-by-one, state machine errors)
- Verifying analysis against specifications or standards
- Complex algorithm review
- Understanding obscure file formats or protocols

## File-Based Pattern

Always use the file-based input/output pattern for reliability. All Codex output must be written to a file to avoid polluting the agent context.

### Helper Script (Recommended)

If you can access the skill directory, use the bundled helper script:

```
scripts/codex-run.sh
```

It creates a per-run temp directory, writes `question.txt`, captures stdout to `reply.txt`, and captures stderr to `stderr.txt`.

Example usage:

```bash
bash scripts/codex-run.sh /path/to/question.txt
# or
bash scripts/codex-run.sh < /path/to/question.txt
```

If you cannot locate the script, follow the manual steps below.

### 0. Create a Per-Run Temp Directory

Create a unique run directory with the format `/tmp/codex-YYYYMMDD-HHMMSS-<rand>/` and store all files there.

Never use `/tmp/question.txt` or `/tmp/reply.txt`.

Example:

```bash
RUN_ID="$(date +%Y%m%d-%H%M%S)-$RANDOM"
RUN_DIR="/tmp/codex-$RUN_ID"
mkdir -p "$RUN_DIR"
QUESTION_FILE="$RUN_DIR/question.txt"
REPLY_FILE="$RUN_DIR/reply.txt"
STDERR_FILE="$RUN_DIR/stderr.txt"
```

### 1. Redact Before Writing

Before writing the prompt, remove or anonymize:

- API keys, tokens, passwords, secrets
- Customer or user data
- Proprietary or licensed code not safe to share

### 2. Write the Question

Create `$QUESTION_FILE` with:

```
[Clear problem statement]

Here is the relevant code:
```[lang]
[paste COMPLETE functions — never truncate]
```

Key observations:
1. [What works]
2. [What fails]
3. [Conditions of failure]

Questions:
1. [Specific question]
2. [Specific question]

Please write a detailed analysis.
The final message must contain the complete answer because only the last message is saved.
```

**Critical:** Include complete functions, not snippets. Mention relevant spec sections if debugging against a standard (JPEG, PNG, HTTP, etc.).

### 3. Invoke Codex

```bash
codex exec -o "$REPLY_FILE" --full-auto < "$QUESTION_FILE" 2> "$STDERR_FILE"
```

- `exec` — non-interactive mode (required for CLI piping)
- `-o $REPLY_FILE` — write output to file (only the last message is saved)
- `--full-auto` — run without interactive prompts
- `2> $STDERR_FILE` — capture stderr to avoid polluting context

### 4. Read and Evaluate

Read `$REPLY_FILE`. Evaluate suggestions critically — verify each finding against authoritative sources before applying. Codex can misidentify correct code as buggy.

If Codex writes reasoning or logs to stderr, keep it in `$STDERR_FILE`. Only open it if needed for debugging.

Respond with a short summary and the path to the output file. Do not paste the full file contents into the response.

### Short Questions

For simple queries without debugging context, still write output to the per-run file:

```bash
RUN_ID="$(date +%Y%m%d-%H%M%S)-$RANDOM"
RUN_DIR="/tmp/codex-$RUN_ID"
mkdir -p "$RUN_DIR"
echo "Explain the JPEG progressive AC refinement algorithm" | codex exec -o "$RUN_DIR/reply.txt" --full-auto 2> "$RUN_DIR/stderr.txt"
```

Prefer the file-based pattern for anything involving code.

## Troubleshooting

| Issue | Fix |
|---|---|
| "stdin is not a terminal" | Use `codex exec`, not bare `codex` |
| No output | Verify `-o` path is writable |
| Reasoning in stderr | Redirect stderr to a file and avoid pasting it into the response |
| Hangs | Ensure `--full-auto` flag is set |

## Iteration

If the first response is insufficient, create a new `$QUESTION_FILE` incorporating what was learned from the previous reply and invoke again in a new per-run directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seruman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
