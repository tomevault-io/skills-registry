---
name: gemini-cli-runtime
description: Internal helper contract for calling the Gemini companion runtime from Claude Code Use when this capability is needed.
metadata:
  author: rsecss
---

# Gemini CLI Runtime Contract

Primary helper:

```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" task "<raw arguments>"
```

## Execution rules

- Use this in the `gemini:gemini-rescue` subagent only.
- Single call per rescue request.
- May use `gemini-prompting` to tighten prompt before call.
- Do not inspect the repo, solve tasks, or analyze.
- Return stdout unchanged.

## Flags

| Flag | Behavior |
|------|----------|
| `--write` | Default add (write-capable) |
| `-m <model>` | Pass through (pro/flash/flash-lite/auto) |
| `--background` | Strip — handled by Claude Code execution |
| `--wait` | Strip — handled by Claude Code execution |

## Output handling

Return the full stdout of the companion script verbatim. Do not parse, summarize, or reformat it.

---
> Source: [rsecss/gemini-plugin-cc](https://github.com/rsecss/gemini-plugin-cc) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
