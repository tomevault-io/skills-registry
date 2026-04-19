---
name: agent-orchestrator-cli
description: Run and operate the local `agent-orchestrator` via `agentd` and `agentctl`, including spawn/send/wait workflows and event inspection. Use when this capability is needed.
metadata:
  author: jeremyhon
---

# Agent Orchestrator CLI

Use this skill when you need to run or validate orchestration flows in this repo.

## Scope

- Start the local daemon.
- Drive sessions/runs/messages from CLI.
- Spawn child agents and wait for completion.
- Inspect event timelines and tool-call events.

## Prerequisites

- Working directory: repo root (`agent-orchestrator`).
- `codex` CLI installed and available on `PATH`.

## Quickstart

1. Start daemon:
   - `./bin/agentd`
   - Auth enabled (single token):
     - `AGENTD_AUTH_TOKEN=dev-token ./bin/agentd`
   - Auth enabled (multiple tokens):
     - `AGENTD_AUTH_TOKENS=token-a,token-b ./bin/agentd`
     - `./bin/agentd --auth-token token-a --auth-token token-b`
   - Optional profile/args defaults:
     - `AGENTD_CODEX_PROFILE=mobile ./bin/agentd`
     - `./bin/agentd --codex-profile mobile --codex-extra-arg=--verbose`
   - Optional concurrent workers:
     - `./bin/agentd --max-concurrency 4`
   - `GET /health` stays unauthenticated; all other endpoints require bearer token when auth is configured.
2. Check daemon/pool status:
   - `AGENTCTL_AUTH_TOKEN=dev-token ./bin/agentctl daemon status`
   - `./bin/agentctl --auth-token dev-token daemon status`
   - `./bin/agentctl daemon status`
   - `./bin/agentctl --field daemon_health daemon status`
   - `./bin/agentctl --field alive_workers daemon status`
   - `./bin/agentctl --field queue_counts.queued_count daemon status`
3. Create session:
   - `SESSION_ID=$(./bin/agentctl --field session.id session create)`
4. Send message:
   - `RUN_ID=$(./bin/agentctl --field run.id message send --session-id "$SESSION_ID" --text "Reply exactly: hi")`
   - Retry-safe send:
     - `RUN_ID=$(./bin/agentctl --field run.id message send --session-id "$SESSION_ID" --text "Reply exactly: hi" --idempotency-key "$(uuidgen)")`
5. Wait:
   - `./bin/agentctl --field run.status run wait --run-id "$RUN_ID"`
6. View events:
   - `./bin/agentctl events list --session-id "$SESSION_ID"`
7. Diagnose a run:
   - `./bin/agentctl run diagnose --run-id "$RUN_ID"`
   - Inspect queue/worker diagnostics:
     - `./bin/agentctl --field diagnostics.worker_id run diagnose --run-id "$RUN_ID"`
     - `./bin/agentctl --field diagnostics.alive_workers run diagnose --run-id "$RUN_ID"`
     - `./bin/agentctl --field diagnostics.queue_counts.queued_count run diagnose --run-id "$RUN_ID"`
     - `./bin/agentctl --field diagnostics.queue_position run diagnose --run-id "$RUN_ID"`
   - Inspect codex invocation defaults:
     - `./bin/agentctl --field diagnostics.codex_invocation.profile run diagnose --run-id "$RUN_ID"`
     - `./bin/agentctl --field diagnostics.codex_invocation.extra_args run diagnose --run-id "$RUN_ID"`

## Agent-to-Agent Flow

1. Spawn child session from parent run:
   - `SPAWN=$(./bin/agentctl tool session-spawn --parent-run-id "$PARENT_RUN_ID" --task "Reply exactly: child-ready")`
2. Extract child IDs:
   - `CHILD_SESSION_ID=$(printf '%s' "$SPAWN" | python3 -c 'import json,sys; print(json.load(sys.stdin)["session"]["id"])')`
   - `CHILD_RUN_ID=$(printf '%s' "$SPAWN" | python3 -c 'import json,sys; print(json.load(sys.stdin)["run"]["id"])')`
3. Wait for child:
   - `./bin/agentctl tool session-wait --run-id "$CHILD_RUN_ID"`
4. Send follow-up to child session:
   - `./bin/agentctl tool session-send --session-id "$CHILD_SESSION_ID" --text "Reply exactly: done"`
5. Escalate approval/permission request from child run to parent session:
   - `./bin/agentctl tool session-reply-parent --run-id "$CHILD_RUN_ID" --request-kind approval_request --text "Need approval to run: <command>"`
   - Retry-safe escalation:
     - `./bin/agentctl tool session-reply-parent --run-id "$CHILD_RUN_ID" --request-kind approval_request --text "Need approval to run: <command>" --idempotency-key "$(uuidgen)"`

## Approval Escalation Flow (Child -> Parent)

1. Child run requests approval without parent session ID:
   - `./bin/agentctl tool session-reply-parent --run-id "$CHILD_RUN_ID" --request-kind approval_request --text "Need approval to use elevated permissions"`
2. Parent observes request in parent session events:
   - `./bin/agentctl events list --session-id "$PARENT_SESSION_ID"`
3. Parent responds back to child:
   - `./bin/agentctl tool session-send --session-id "$CHILD_SESSION_ID" --text "Approved. Continue with guardrails."`

## Useful Output Flags

- Compact JSON:
  - `./bin/agentctl --format json session list`
- Extract a single field:
  - `./bin/agentctl --field session.id session create`
  - `./bin/agentctl --field run.id message send --session-id "$SESSION_ID" --text "hi"`
- Tail one event field:
  - `./bin/agentctl --field event_type events tail --session-id "$SESSION_ID" --follow`

## Best Practices

- Preflight checks:
  - Start with `./bin/agentctl daemon status`.
  - Fail fast if `daemon_health != ok` or `alive_workers == 0`.
- Script-safe output:
  - Prefer `--field` or `--format json` for automation.
  - Avoid parsing pretty output in scripts.
- Run discipline:
  - Capture `SESSION_ID` and `RUN_ID` immediately after create/send.
  - Use explicit wait bounds (`run wait --timeout-s ...`).
  - If a task should commit, say it explicitly in the prompt.
  - For retryable network calls, pass `--idempotency-key` and reuse the same value when retrying.
- Concurrency safety:
  - Do not edit the same worktree while a spawned coding run is active.
  - For parallel work, use separate git worktrees per spawned run.
  - If agent reports unexpected workspace changes, stop and decide whether to ignore or include/review those files.
- Troubleshooting order:
  - `./bin/agentctl run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.likely_issue run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.worker_id run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.worker_alive run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.queue_counts.queued_count run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.queue_position run diagnose --run-id "$RUN_ID"`
- Post-run verification:
  - Confirm terminal status from API (`run get`/`run wait`), not assumptions.
  - For commit-required tasks, compare `git rev-parse HEAD` before/after and check `git log -1 --oneline`.
  - Run `make test` before committing code changes.

## Troubleshooting

- If runs stay `queued`, confirm `agentd` is running and worker started.
- If runs fail with backend errors, check `backend.event` and `run.error` entries:
  - `./bin/agentctl events list --session-id "$SESSION_ID"`
- If a newly documented command returns `404 not_found` but health is OK, daemon is likely stale:
  - `curl -sS http://127.0.0.1:8765/health`
  - If health is OK, restart `agentd` from current repo checkout.
- For stuck `running` runs, use diagnose:
  - `./bin/agentctl run diagnose --run-id "$RUN_ID"`
  - `./bin/agentctl --field diagnostics.likely_issue run diagnose --run-id "$RUN_ID"`
- If testing cancel cascade, cancel a parent run:
  - `./bin/agentctl run transition --run-id "$PARENT_RUN_ID" --to-status canceled`

## Maintenance Rule

When `agentctl` behavior/flags/commands change, update this skill in the same change.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremyhon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
