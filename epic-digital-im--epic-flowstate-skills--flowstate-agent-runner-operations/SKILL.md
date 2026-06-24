---
name: flowstate-agent-runner-operations
description: Use when any FlowState agent harness is running inside an agent sidecar and needs to inspect, monitor, start, stop, resume, cancel, or debug the Rust process runner - provides the in-container runner control workflow and safety rules
metadata:
  author: epic-digital-im
---

# Agent Runner Operations

**Status:** Active
**Purpose:** Teach FlowState agent harnesses how to operate the co-resident `flowstate-runner` Rust daemon inside their agent container
**Scope:** Agent sidecars that run the Rust process execution engine, regardless of chat or coding harness
**Trigger:** Agent needs to monitor or control process executions assigned to its team member ID
**Input:** Running agent container with `FLOWSTATE_AGENT_ID`, FlowState CLI, and `flowstate-runner`
**Output:** Runner status understood, assigned process executions advanced or safely paused/cancelled, and failures diagnosed

---

## Overview

FlowState agents do not manually walk process steps unless the runner is unavailable or a step explicitly asks for agent reasoning. The Rust runner is the primary process driver. Agents monitor the runner, create or assign work in FlowState, trigger scans/resumes, and handle the small number of inference-heavy `agent-task` steps only when the process engine delegates them.

```
Verify Scope -> Check Runner -> List Executions -> Inspect Work
     (0)            (1)              (2)              (3)
                                                        |
                                                        v
Act on Execution <- Diagnose Failure <- Resume/Scan <- Observe Pause
      (6)                (5)             (4)            (4)
```

---

## Architecture

Each agent sidecar runs two long-lived services under s6:

| Service | Purpose |
| ------- | ------- |
| agent gateway | Chat/tool harness process, such as OpenClaw, Hermes, or another compatible runtime. The agent uses this process to think, answer, and call tools. |
| `flowstate-runner` | Rust daemon that scans FlowState, creates `processexecutions`, resumes paused executions, and executes steps. |

The runner is scoped by environment:

| Env var | Required | Purpose |
| ------- | -------- | ------- |
| `FLOWSTATE_AGENT_ID` | Yes | Team member ID this runner is allowed to execute for. |
| `FLOWSTATE_AGENT_NAME` | No | Human-readable agent name used in logs and worker heartbeat metadata. |
| `RUNNER_CONTROL_TOKEN` | Yes for control API | Bearer token used by `flowstate runner ...`; generated into `/run/runner.token`. |
| `RUNNER_CONTROL_URL` | No | Control API URL. Defaults to `http://localhost:9090`. |
| `RUNNER_MAX_CONCURRENT` | No | Concurrent execution limit. Defaults to `5`. |

The runner enforces scope in three places:

1. Scanner looks for entity-triggered processes assigned to `metadata.assignedAgent = FLOWSTATE_AGENT_ID`.
2. Resumer looks for paused executions with `variables.assignedAgent = FLOWSTATE_AGENT_ID`.
3. Run guard refuses to execute a record assigned to a different team member.

---

## Step 0: Verify Container Scope

**Who:** FlowState agent
**Pause:** No

### Actions

1. Read the configured agent identity:
   ```bash
   printf 'FLOWSTATE_AGENT_ID=%s\nFLOWSTATE_AGENT_NAME=%s\n' "$FLOWSTATE_AGENT_ID" "$FLOWSTATE_AGENT_NAME"
   ```
2. Confirm runner tools exist:
   ```bash
   command -v flowstate
   command -v flowstate-runner
   ```
3. Confirm the control token is available to the agent runtime user:
   ```bash
   test -s /run/runner.token && echo "runner token present"
   ```
4. If `RUNNER_CONTROL_TOKEN` is not exported in the shell, export it from the token file for this session:
   ```bash
   export RUNNER_CONTROL_TOKEN="$(cat /run/runner.token)"
   export RUNNER_CONTROL_URL="${RUNNER_CONTROL_URL:-http://localhost:9090}"
   ```

### Done when

- `FLOWSTATE_AGENT_ID` is non-empty
- `flowstate` and `flowstate-runner` are on `PATH`
- `RUNNER_CONTROL_TOKEN` is available for CLI control commands

---

## Step 1: Check Runner Health

**Who:** FlowState agent
**Pause:** No

### Actions

1. Use the FlowState CLI first:
   ```bash
   flowstate runner status --format json
   ```
2. Confirm the response includes:
   - `status: "ok"`
   - `agentId` matching `FLOWSTATE_AGENT_ID`
   - `runningExecutions`, possibly empty
   - `lastActivityAt`, if the runner has found work
3. If the CLI fails, check the raw health endpoint:
   ```bash
   curl -fsS http://localhost:9090/health | jq .
   ```
4. If health fails, inspect s6 service state and recent logs:
   ```bash
   s6-rc -a list | grep flowstate-runner
   s6-svstat /run/service/flowstate-runner
   tail -200 /run/service/flowstate-runner/log/current 2>/dev/null || true
   ```

### Done when

- Runner health is reachable
- Agent scope is verified
- Any active execution IDs are known

---

## Step 2: List Assigned Executions

**Who:** FlowState agent
**Pause:** No

### Actions

1. List executions controlled by this runner:
   ```bash
   flowstate runner ls
   ```
2. For machine-readable inspection:
   ```bash
   flowstate runner ls --format json
   ```
3. Filter by status when triaging:
   ```bash
   flowstate runner ls --status running
   flowstate runner ls --status paused
   flowstate runner ls --status failed
   flowstate runner ls --status completed --limit 20
   ```
4. Treat an empty list as "no scoped executions visible", not as failure. Trigger a scan if new assigned work should exist.

### Done when

- The agent knows which scoped executions exist and which need attention

---

## Step 3: Inspect an Execution

**Who:** FlowState agent
**Pause:** No

### Actions

1. Show the execution:
   ```bash
   flowstate runner show <execution_id> --format json
   ```
2. Inspect:
   - `processId`
   - `status`
   - `currentStepId`
   - `variables.assignedAgent`
   - `variables.entityId`, `entityType`, `entityCollection`
   - `stepHistory`
   - `error`
3. Confirm `variables.assignedAgent` matches `FLOWSTATE_AGENT_ID`. If it does not, do not try to bypass the guard.
4. If a process or step needs direct DB inspection, use FlowState collection tools with real IDs from `.flowstate/config.json`; never guess IDs.

### Done when

- Current process, step, entity, pause/failure reason, and ownership are understood

---

## Step 4: Trigger Scan or Resume

**Who:** FlowState agent
**Pause:** No

### Actions

1. Trigger a scan when an entity was newly assigned or tagged:
   ```bash
   flowstate runner scan
   ```
   This schedules work and returns quickly. Check `flowstate runner ls` afterwards.
2. Trigger a resume cycle when approvals, human-task responses, or pause conditions were satisfied:
   ```bash
   flowstate runner resume
   ```
3. Force a single paused execution back to pending only when the pause condition is genuinely satisfied or the operator intentionally wants to retry:
   ```bash
   flowstate runner resume <execution_id>
   ```

### Done when

- New eligible work has a `processexecutions` row, or paused eligible work has moved to `pending`/`running`

---

## Step 5: Diagnose Stalls and Failures

**Who:** FlowState agent
**Pause:** No

### Actions

1. For a paused execution, inspect the pause reason:
   ```bash
   flowstate runner show <execution_id> --format json | jq '.metadata._pause_reason, .currentStepId, .variables'
   ```
2. If waiting on an approval, query the approval record shown in variables or pause metadata and look for `status`, `response`, and `comments`.
3. For a failed execution, inspect:
   ```bash
   flowstate runner show <execution_id> --format json | jq '.error, .stepHistory[-5:]'
   ```
4. Try runner logs, but know the current `/executions/:id/logs` endpoint may return a placeholder until OBS proxying is fully wired:
   ```bash
   flowstate runner logs <execution_id>
   ```
5. Fall back to container logs when needed:
   ```bash
   ps -ef | grep flowstate-runner
   s6-svstat /run/service/flowstate-runner
   ```

### Done when

- The agent can state whether the execution is waiting, failed due to a step error, mis-scoped, or blocked by runner health/configuration

---

## Step 6: Pause, Cancel, or Restart Safely

**Who:** FlowState agent
**Pause:** Maybe

### Actions

1. Pause only when work must stop without losing persisted state:
   ```bash
   flowstate runner pause <execution_id> --reason "manual: <short reason>"
   ```
2. Cancel only when the execution should be marked failed and should not continue:
   ```bash
   flowstate runner cancel <execution_id> --reason "manual: <short reason>"
   ```
3. Restart the runner service only for runner process health issues, not normal paused work:
   ```bash
   s6-svc -r /run/service/flowstate-runner
   ```
4. After any pause/cancel/restart, re-check:
   ```bash
   flowstate runner status --format json
   flowstate runner ls --format json
   ```

### Done when

- The execution state in FlowState matches the intended operator action
- The runner is healthy after service-level intervention

---

## Creating Runner-Eligible Work

Agents create process-driven work by creating or updating FlowState entities so scanner triggers can match them.

Required entity metadata for agent-scoped process triggers:

```json
{
  "metadata": {
    "assignedAgent": "<FLOWSTATE_AGENT_ID or target team member id>"
  },
  "tags": ["brainstorm"]
}
```

Guidelines:

- Set `metadata.assignedAgent` to the target agent team member ID before triggering process scans.
- Include the process trigger tag or status expected by the registered process, such as `brainstorm`.
- For CEO-to-CTO work, the CEO-created entity should have `metadata.assignedAgent` set to the CTO team member ID and any approver variables set to the CEO team member ID when the process supports peer approval routing.
- After creating or updating the entity, the target agent runs `flowstate runner scan`.

---

## Manual Execution Fallback

Manual MCP process execution is the fallback path, not the default.

Use `flowstate-process-execution` only when:

- `flowstate-runner` is unavailable and cannot be restarted
- A process definition is being debugged before runner registration
- The runner fails on a step and you need to reproduce the step by hand
- A human explicitly asks for a manual process walkthrough

When using the fallback, record what was done in the execution, entity discussion, or approval trail so the runner and future agents have durable context.

---

## Error Handling

| Situation | Action |
| --------- | ------ |
| `FLOWSTATE_AGENT_ID` missing | Do not run unscoped. Fix container/orchestrator env and restart the sidecar. |
| `flowstate runner status` returns 401 | Export `RUNNER_CONTROL_TOKEN="$(cat /run/runner.token)"` and retry. |
| `flowstate runner status` returns 503 | Runner booted without `RUNNER_CONTROL_TOKEN`; check `runner-token` s6 service and restart `flowstate-runner`. |
| `flowstate runner show` returns 403 | Execution belongs to another agent; stop and route to the assigned agent. |
| New work not discovered | Confirm entity trigger fields, especially `metadata.assignedAgent`, status, tags, `orgId`, and `workspaceId`; then run `flowstate runner scan`. |
| Execution stuck paused | Inspect `_pause_reason`, approvals, and current step; resume only after the condition is satisfied. |
| Runner repeatedly exits idle | Check `IDLE_TIMEOUT_SECS`; durable/manual agent containers usually set `IDLE_TIMEOUT_SECS=0`. |

---

## Command Reference

| Command | Purpose |
| ------- | ------- |
| `flowstate runner status --format json` | Health, agent scope, running execution IDs. |
| `flowstate runner ls [--status <status>] [--format json]` | List scoped process executions. |
| `flowstate runner show <id> --format json` | Inspect one execution. |
| `flowstate runner scan` | Schedule immediate scanner pass. |
| `flowstate runner resume` | Schedule immediate resumer pass. |
| `flowstate runner resume <id>` | Move one scoped execution back to pending. |
| `flowstate runner pause <id> --reason "..."` | Mark one scoped execution paused. |
| `flowstate runner cancel <id> --reason "..."` | Mark one scoped execution failed. |
| `flowstate runner logs <id>` | Execution log endpoint; may be placeholder until OBS proxy is wired. |
| `flowstate-runner --project-root <agent_home> scan` | Direct binary scanner, mostly for debugging. Current OpenClaw sidecars use `/home/openclaw`. |
| `flowstate-runner --project-root <agent_home> resume` | Direct binary resumer, mostly for debugging. Current OpenClaw sidecars use `/home/openclaw`. |
| `flowstate-runner --project-root <agent_home> run <id>` | Direct binary execution, guarded by agent scope. Current OpenClaw sidecars use `/home/openclaw`. |

---

## Safety Rules

- Do not clear or forge `variables.assignedAgent` to bypass scope.
- Do not operate on another agent's execution from this container.
- Do not mutate `processexecutions` directly. Use runner commands; if a repair path is missing, record a tool-surface gap before changing process execution state.
- Prefer scan/resume over direct `flowstate-runner run <id>` during normal operations.
- Keep inference work inside `agent-task` steps or explicit human requests; use CLI/MCP commands for deterministic process actions.

---

_Created: 2026-05-24_

---
> Source: [epic-digital-im/epic-flowstate-skills](https://github.com/epic-digital-im/epic-flowstate-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
