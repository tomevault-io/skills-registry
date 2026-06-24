---
name: quest
description: > Use when this capability is needed.
metadata:
  author: sailscastshq
---

# Quest — Background Job Scheduling

Quest is a job scheduling hook for Sails.js that turns scripts in the `scripts/` directory into scheduled background jobs. Each job runs as an isolated child process via `sails run`, with full access to models, helpers, and configuration.

## When to Use

Use this skill when:

- Creating background jobs or scheduled tasks
- Setting up cron schedules, recurring intervals, or one-time delayed execution
- Defining job scripts with inputs and overlap prevention
- Using the `sails.quest` API to manage jobs at runtime
- Listening to job lifecycle events (start, complete, error)
- Configuring the console environment for lightweight job execution

## Rules

Read individual rule files for detailed explanations and code examples:

- [rules/getting-started.md](rules/getting-started.md) - What Quest is, installation, project structure, quick start
- [rules/scheduling.md](rules/scheduling.md) - Cron, human-readable intervals, shorthand, later.js, one-time execution
- [rules/job-definition.md](rules/job-definition.md) - Script anatomy, inputs, overlap prevention, config-defined jobs
- [rules/api.md](rules/api.md) - `sails.quest` API reference: control, info, events
- [rules/patterns.md](rules/patterns.md) - Common job patterns with complete script examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sailscastshq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
