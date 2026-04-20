---
name: cron-scheduler
description: Schedule future tasks for the agent. Use when the user wants reminders, periodic fetches, or recurring actions. The agent sets a timer for itself and gets "woken up" at the scheduled time. Use when this capability is needed.
metadata:
  author: alfredzhang98
---

# Cron Scheduler

Schedule one-time or recurring tasks. The agent can set "alarms" for itself — when the time comes, the agent is woken up and processes the task through the normal auto-reply loop.

Aligned with OpenClaw's 3-type cron system.

## Schedule Types

### 1. One-shot (`at`) — Fire once at a specific time

```
cron_schedule --at "2h" --task "Remind me to check the Pull Request"
cron_schedule --at "2026-02-15T14:30:00" --task "Submit the report"
```

Accepts relative durations (`30m`, `2h`, `1d`) or absolute ISO timestamps.

### 2. Recurring interval (`every`) — Fire at fixed intervals

```
cron_schedule --every "30m" --task "Check deployment status"
cron_schedule --every "6h" --task "Sync upstream changes"
```

### 3. Cron expression (`cron`) — Standard cron with timezone

```
cron_schedule --cron "0 8 * * *" --tz "Asia/Shanghai" --task "Fetch morning news"
cron_schedule --cron "0 18 * * 5" --task "Generate weekly report"
```

Standard 5-field: `minute hour day-of-month month day-of-week`

## Management

```
cron_list                    # List all scheduled jobs
cron_status --id <job-id>    # Check a specific job
cron_remove --id <job-id>    # Cancel a job
cron_run --id <job-id>       # Force-run a job immediately
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `--at` | duration or ISO time | no | One-shot schedule |
| `--every` | duration | no | Recurring interval |
| `--cron` | cron expression | no | Cron schedule |
| `--tz` | timezone | no | Timezone for cron (default: system local) |
| `--task` | string | yes | What to do when the timer fires |
| `--id` | string | no | Job ID (for status/remove/run) |

Exactly one of `--at`, `--every`, or `--cron` must be provided.

## Delivery

When a job fires:
1. **systemEvent** mode: Injects text into the main session (default)
2. **agentTurn** mode: Runs agent with the task message in an isolated session

Results are sent to the user via Telegram.

## Examples

- "2 hours from now, remind me to check this PR" → `cron_schedule --at "2h" --task "Check PR #42"`
- "Every 30 minutes, check if CI passed" → `cron_schedule --every "30m" --task "Check CI status"`
- "Every morning at 8am, fetch news" → `cron_schedule --cron "0 8 * * *" --task "Fetch and summarize tech news"`
- "Every Friday evening, generate a weekly report" → `cron_schedule --cron "0 18 * * 5" --task "Generate weekly activity report"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alfredzhang98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
