---
name: gemini-cli-runtime
description: Internal helper contract for calling the gemini-companion runtime from Claude Code Use when this capability is needed.
metadata:
  author: seungmanchoi
---

# Gemini Runtime

Use this skill only inside the `gemini:gemini-rescue` subagent.

Primary helper:
- `node "${CLAUDE_PLUGIN_ROOT}/scripts/gemini-companion.mjs" task "<raw arguments>"`

Execution rules:
- The rescue subagent is a forwarder, not an orchestrator. Its only job is to invoke `task` once and return that stdout unchanged.
- Prefer the helper over hand-rolled `git`, direct Gemini CLI strings, or any other Bash activity.
- Do not call `setup`, `review`, `adversarial-review`, `status`, `result`, or `cancel` from `gemini:gemini-rescue`.
- Use `task` for every rescue request, including diagnosis, planning, research, and explicit fix requests.
- You may use the `gemini-prompting` skill to rewrite the user's request into a tighter Gemini prompt before the single `task` call.
- That prompt drafting is the only Claude-side work allowed. Do not inspect the repo, solve the task yourself, or add independent analysis outside the forwarded prompt text.
- Leave `--thinking` unset unless the user explicitly requests a specific level. The flag is parsed but currently behaves as a one-shot/one-time hint; the runtime does not yet ship a per-invocation thinking override. For persistent control, set the default in `settings.json` via `/gemini:setup`.
- The default model is `auto-gemini-3`. Add `--model` only when the user explicitly asks for one. Aliases include `pro`, `auto-gemini-2.5`, `flash`, `flash-lite`.
- If the user asks for `flash`, map that to `--model gemini-3-flash-preview`.
- Default to a write-capable Gemini run by adding `--write` unless the user explicitly asks for read-only behavior or only wants review, diagnosis, or research without edits.

Command selection:
- Use exactly one `task` invocation per rescue handoff.
- If the forwarded request includes `--background` or `--wait`, treat that as Claude-side execution control only. Strip it before calling `task`, and do not treat it as part of the natural-language task text.
- If the forwarded request includes `--model`, pass it through to `task`.
- If the forwarded request includes `--thinking`, pass it through to `task`. Accepted values are `off`, `low`, `medium`, `high`.
- If the forwarded request includes `--stream-output`, pass it through. Default (no flag) uses compact stderr markers.
- If the forwarded request includes `--resume`, strip that token from the task text and add `--resume-last`.
- If the forwarded request includes `--fresh`, strip that token from the task text and do not add `--resume-last`.
- `--resume`: always use `task --resume-last`, even if the request text is ambiguous.
- `--fresh`: always use a fresh `task` run, even if the request sounds like a follow-up.
- `task --resume-last`: internal helper for "keep going", "resume", "apply the top fix", or "dig deeper" after a previous rescue run.

Safety rules:
- Default to write-capable Gemini work in `gemini:gemini-rescue` unless the user explicitly asks for read-only behavior.
- Preserve the user's task text as-is apart from stripping routing flags.
- Do not inspect the repository, read files, grep, monitor progress, poll status, fetch results, cancel jobs, summarize output, or do any follow-up work of your own.
- Return the stdout of the `task` command exactly as-is.
- If the Bash call fails or Gemini cannot be invoked, return nothing.

## Job health labels

`/gemini:status` and the companion record a `health` field on every tracked job. When you see a job in any of these states, follow the recommended action:

- `active` — Gemini is producing output recently. Recommended action: wait; do not cancel or poll aggressively.
- `quiet` — No recent progress chunk but the worker is alive and emitting heartbeats. Recommended action: let it run; check again with `/gemini:status` after a minute or two.
- `possibly_stalled` — Both progress and heartbeat have aged out. Recommended action: run `/gemini:status <job-id>` to inspect recent events; if stuck, `/gemini:cancel <job-id>` and retry.
- `rate_limited` — Gemini reported a rate-limit diagnostic. Recommended action: back off, retry later, or switch model with `--model`.
- `auth_required` — Gemini is missing credentials or the session expired. Recommended action: re-run `/gemini:setup` and authenticate (`!gemini` or `GEMINI_API_KEY`).
- `broker_unhealthy` — The ACP broker process is misbehaving. Recommended action: cancel the job and start a new one; if it persists, restart the Claude session to recycle the broker.
- `worker_missing` — A job is marked running but the worker process is gone. Recommended action: `/gemini:cancel <job-id>` to clear it, then start a fresh task.
- `failed` — Job terminated with an error. Recommended action: read `/gemini:result <job-id>` for the failure event and decide whether to retry.
- `completed` — Job finished successfully. Recommended action: fetch the result with `/gemini:result <job-id>`.
- `cancelled` — Job was cancelled by the user or the system. Recommended action: start a new task if needed; do not fabricate output for a cancelled run.

---
> Source: [seungmanchoi/claude-gemini-plugin](https://github.com/seungmanchoi/claude-gemini-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
