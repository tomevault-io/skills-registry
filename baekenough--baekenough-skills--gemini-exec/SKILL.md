---
name: gemini-exec
description: Execute Google Gemini CLI prompts in non-interactive mode and return structured results. Enables hybrid AI workflows combining your AI agent's reasoning with Gemini's code generation. Supports text, JSON, and stream-JSON output, approval modes (normal, yolo, sandbox, plan), model override, timeout control, and working directory selection. Use for code generation, research, analysis, and reasoning tasks via Google Gemini CLI. Use when this capability is needed.
metadata:
  author: baekenough
---

# Gemini Exec Skill

Execute Google Gemini CLI prompts in non-interactive mode and return structured results. Enables hybrid workflows combining any AI coding agent with Gemini's code generation and reasoning capabilities.

## Prerequisites

- **Node.js 18+** required
- **Gemini CLI** installed: `npm install -g @anthropic-ai/gemini-cli` or via [GitHub releases](https://github.com/google-gemini/gemini-cli)
- **Authentication**: Set `GOOGLE_API_KEY` or `GEMINI_API_KEY` environment variable, or authenticate via `gcloud auth`

### Installation

Install this skill via skills.sh:

```bash
npx skills add baekenough/baekenough-skills --skill gemini-exec
```

**Compatibility**: Works with Claude Code, Cursor, Windsurf, and any AI coding agent that supports the skills.sh standard.

## Options

```
--prompt <text>   Required. The prompt to send to Gemini CLI
--json            Return structured JSON output (-o json)
--stream-json     Return streaming JSON events (-o stream-json)
--output <path>   Save response to file
--model <name>    Model override (default: Gemini CLI default)
--timeout <ms>    Execution timeout (default: 120000, max: 600000)
--yolo            Enable auto-approval mode (gemini -y)
--sandbox         Run in sandbox mode (gemini -s)
--plan            Use plan approval mode (--approval-mode plan)
--working-dir     Working directory for Gemini execution
```

## Workflow

```
1. Pre-checks
   - Verify `gemini` binary is installed (which gemini)
   - Verify authentication (GOOGLE_API_KEY, GEMINI_API_KEY, or gcloud auth)
2. Build command
   - Base: gemini -p "<prompt>"
   - Apply options: -o json, -m <model>, -y, -s, --approval-mode plan
3. Execute
   - Run via Bash tool with timeout (default 2min, max 10min)
   - Or use helper script: node scripts/gemini-wrapper.cjs
4. Parse output
   - Text mode: return raw stdout
   - JSON mode: parse single JSON object, extract response field
   - Stream-JSON mode: parse event stream, extract final assistant message
5. Report results
   - Format output with execution metadata
```

## Safety Defaults

- `-p` flag: Non-interactive prompt mode (no session persistence)
- Default mode: Normal approval (Gemini prompts for confirmation)
- Override with `--yolo` only when the task and environment are trusted
- Sandbox mode (`-s`) available for isolated execution

## Output Format

### Success (Text Mode)

```
[Gemini Exec] Completed

Model: (default)
Duration: 23.4s
Working Dir: /path/to/project

--- Output ---
{gemini response text}
```

### Success (JSON Mode)

```
[Gemini Exec] Completed (JSON)

Model: (default)
Duration: 23.4s

--- Response ---
{extracted response from JSON}

--- Stats ---
{token usage and other stats}
```

### Success (Stream-JSON Mode)

```
[Gemini Exec] Completed (Stream-JSON)

Model: (default)
Duration: 23.4s
Events: 12

--- Final Message ---
{extracted final assistant message}
```

### Failure

```
[Gemini Exec] Failed

Error: {error_message}
Exit Code: {code}
Suggested Fix: {suggestion}
```

## Helper Script

For complex executions, use the wrapper script:

```bash
node scripts/gemini-wrapper.cjs --prompt "your prompt" [options]
```

The wrapper provides:
- Environment validation (binary + auth checks)
- Safe command construction
- JSON and stream-JSON parsing with response extraction
- Structured JSON output
- Timeout handling with graceful termination

## Examples

```bash
# Simple text prompt
node scripts/gemini-wrapper.cjs --prompt "explain what this project does"

# JSON output with model override
node scripts/gemini-wrapper.cjs --prompt "list all TODO items" --json --model gemini-2.5-pro

# Stream-JSON for detailed event tracking
node scripts/gemini-wrapper.cjs --prompt "analyze the codebase" --stream-json

# Save output to file
node scripts/gemini-wrapper.cjs --prompt "generate a README" --output ./README.md

# Sandbox mode with auto-approval
node scripts/gemini-wrapper.cjs --prompt "fix the failing tests" --yolo --sandbox

# Plan mode for careful execution
node scripts/gemini-wrapper.cjs --prompt "refactor the auth module" --plan

# Specify working directory
node scripts/gemini-wrapper.cjs --prompt "analyze the codebase" --working-dir /path/to/project
```

## Availability Check

This skill requires the Gemini CLI binary to be installed and authenticated. It is only usable when:

1. `gemini` binary is found in PATH (`which gemini` succeeds)
2. Authentication is valid (`GOOGLE_API_KEY` or `GEMINI_API_KEY` is set, or `gcloud auth` is active)

If either check fails, the skill cannot execute. Fall back to your agent's native web search or reasoning tools for the task.

## Approval Modes

| Mode | Flag | Behavior | Use Case |
|------|------|----------|----------|
| Normal | _(default)_ | Gemini prompts for confirmation on actions | General tasks, untrusted environments |
| Yolo | `--yolo` | Auto-approve all actions without confirmation | Trusted tasks, CI pipelines |
| Sandbox | `--sandbox` | Run in isolated sandbox environment | Untrusted code, experimentation |
| Plan | `--plan` | Require plan approval before execution | Careful refactoring, production changes |

---
> Source: [baekenough/baekenough-skills](https://github.com/baekenough/baekenough-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
