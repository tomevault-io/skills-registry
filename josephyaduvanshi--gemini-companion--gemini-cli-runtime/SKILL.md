---
name: gemini-cli-runtime
description: Internal helper contract for calling the gemini-companion runtime from Claude Code Use when this capability is needed.
metadata:
  author: josephyaduvanshi
---

# Gemini Runtime

Use this skill only inside the `gemini:gemini-rescue` subagent.

Primary helper:
- `node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" task "<raw arguments>"`

Execution rules:
- The rescue subagent is a forwarder, not an orchestrator. Its only job is to invoke `task` once and return that stdout unchanged.
- Prefer the helper over hand-rolled `git`, direct `gemini` CLI strings, or any other Bash activity.
- Do not call `setup`, `status`, `result`, or `cancel` from `gemini:gemini-rescue`.
- Use `task` for every rescue request, including diagnosis, planning, research, and explicit fix requests.
- You may use the `gemini-prompting` skill to rewrite the user's request into a tighter Gemini prompt before the single `task` call.
- That prompt drafting is the only Claude-side work allowed. Do not inspect the repo, solve the task yourself, or add independent analysis outside the forwarded prompt text.
- Leave `--model` unset by default. Add `--model` only when the user explicitly asks for one.
- Map `pro` → `gemini-2.5-pro`, `flash` → `gemini-2.5-flash`, `flash-lite` → `gemini-2.5-flash-lite`. The companion does this translation for you, so passing the alias through is fine.

Command selection:
- Use exactly one `task` invocation per rescue handoff.
- If the forwarded request includes `--model`, pass it through to `task`.
- Default to a write-capable Gemini run by adding `--write` unless the user explicitly asks for read-only behavior.

Safety rules:
- Preserve the user's task text as-is apart from stripping routing flags.
- Do not inspect the repository, read files, grep, monitor progress, poll status, fetch results, cancel jobs, summarize output, or do any follow-up work of your own.
- Return the stdout of the `task` command exactly as-is.
- If the Bash call fails or Gemini cannot be invoked, return nothing.

Effort budgets:
- `--effort` is honored. The companion writes a temp `settings.json` with the corresponding `maxSessionTurns` and points Gemini at it via `GEMINI_CLI_SYSTEM_SETTINGS_PATH`.
- The system-prompt addendum (effort hint, output schema reminder) is prepended to the user prompt because Gemini has no `--append-system-prompt` flag and `GEMINI_SYSTEM_MD` is a full override.

Background and resume:
- `--background` enqueues a detached worker; check `/gemini:status` for progress.
- `--resume-last` (or `--resume`) reuses the most recent Gemini thread for this workspace + Claude session, including sessions started outside the plugin (scanned from `~/.gemini/tmp/<bucket>/chats/`).

---
> Source: [josephyaduvanshi/gemini-companion](https://github.com/josephyaduvanshi/gemini-companion) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
