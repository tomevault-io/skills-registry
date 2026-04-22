---
name: codex-research
description: | Use when this capability is needed.
metadata:
  author: signalcompose
---

# Codex Research Skill

This skill provides integration with OpenAI Codex CLI for research and code review tasks.

## Prerequisites

- Codex CLI installed: `npm install -g @openai/codex`
- Authentication (one of):
  - Run `codex` to complete OAuth authentication, OR
  - Set `OPENAI_API_KEY` environment variable

## Available Scripts

All scripts are located in `${CLAUDE_PLUGIN_ROOT}/scripts/`.

### 1. Check Installation

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/check-codex.sh
```

Verifies Codex CLI is installed and authentication is configured (OAuth or API key).

### 2. Research Mode (codex exec)

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh "your research question"
```

Use for:
- Technical research questions
- Documentation lookup
- Concept explanations
- Best practices inquiry

Example:
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/codex-exec.sh "What is dependency injection and when should I use it?"
```

### 3. Code Review Mode

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/codex-review.sh <target>
```

Options:
- `--staged` - Review all uncommitted changes (staged + unstaged)
- `<file>` - Review specific file (uses `codex review <file>`)
- `<directory>` - Review directory (uses `codex review <dir>`)

**Important**: `--staged` uses `codex exec review uncommitted` which reviews **all uncommitted changes**, not just staged files. To review only specific files, use the file/directory target.

Examples:
```bash
${CLAUDE_PLUGIN_ROOT}/scripts/codex-review.sh --staged
${CLAUDE_PLUGIN_ROOT}/scripts/codex-review.sh src/main.ts
${CLAUDE_PLUGIN_ROOT}/scripts/codex-review.sh ./lib
```

## Workflow

1. First, verify Codex CLI is available using `check-codex.sh`
2. For research tasks, use `codex-exec.sh` with the research prompt
3. For code review, use `codex-review.sh` with the target

## Error Handling

- **Timeout**: Commands timeout after 120 seconds
- **Not installed**: Provides installation instructions
- **Not authenticated**: Prompts to complete OAuth or set OPENAI_API_KEY

## Notes

This skill uses `context: fork` to isolate large outputs from the main conversation context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/signalcompose) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
