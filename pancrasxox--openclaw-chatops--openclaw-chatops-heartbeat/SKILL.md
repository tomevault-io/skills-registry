---
name: openclaw-chatops-heartbeat
description: Maintain a Discord ChatOps workspace rhythm using OpenClaw heartbeat: (1) keep a pinned index of active task threads for visibility, and (2) post periodic digests with Progress/Next/Blockers/Needs-user. Use when a user asks for “定时任务/heartbeat 节奏/索引/汇总”, “keep threads visible”, “daily/commute digest”, or to make the workflow stable and non-spammy. Use when this capability is needed.
metadata:
  author: pancrasxox
---

# OpenClaw ChatOps — Heartbeat Rhythm (Discord)

Implement the stable cadence for a single-user + AI team workflow.

This skill defines **what heartbeat should do** in the ChatOps workspace:
- Index (visibility)
- Digest (cadence)

## Preconditions

- The workspace uses threads as the task unit.
- `updates` exists (or a designated output channel exists).

If the workspace is not initialized, suggest running the init skill.

State semantics (canonical): see `docs/index-and-status.md`.
Tooling/permission notes: see `docs/openclaw-tooling-notes.md`.

## Output type A: Index (visibility)

Goal: Make active threads visible even if people miss thread notifications.

Procedure:
1) Identify active task threads across `work`/`qa`/`research`.
2) Apply state semantics from `docs/index-and-status.md`:
   - Active: `[WIP]`, `[BLOCKED]`
   - Recent done: `[DONE]`
   - Exclude: `[ARCHIVE]`
3) Produce a short index message with links to threads.
4) Pin/refresh the index in `updates`.

Index format (bullet-only):
- WIP:
  - <thread link> — short title
- BLOCKED:
  - <thread link> — blocker summary
- DONE (recent):
  - <thread link>

Noise control:
- Do not post/update if nothing changed since the last index update.
- If many "false alarm" threads appear, treat it as a signal to tighten intake questions and archive quickly.

## Output type B: Digest (cadence)

Goal: Provide periodic “what changed” without requiring manual prompting.

Procedure:
1) For each active thread with new activity since last digest, summarize:
   - Progress
   - Next
   - Blockers
   - Needs-user (questions/decisions)
2) Post a digest to `updates` with links.

Digest format:
- Scope/time window (best-effort)
- For each thread:
  - Link + 1-line progress
  - Next:
  - Blockers:
  - Needs-user:

Noise control:
- Skip digest if there is no new information.
- Prefer fewer messages; do not spam.

## User control (natural language)

Accept and apply user intent like:
- Change cadence (“每 30 分钟更新索引”) — acknowledge and describe what will happen.
- Pause/resume digests.
- Restrict digest windows (e.g., commute time).

Command-style (optional):
- Skill slash command: `/openclaw_chatops_heartbeat <request>`
- Generic runner: `/skill openclaw-chatops-heartbeat <request>`

## Tooling notes

- Use Discord message actions (pins/threads/messages) as available.
- Avoid destructive actions (unpin/delete) unless explicitly requested.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pancrasxox) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
