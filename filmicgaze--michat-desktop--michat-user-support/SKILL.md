---
name: michat-user-support
description: Help the user troubleshoot and operate MiChat (the current environment) by checking gates (session reset, working folder, permissions, credentials) and using the global MiChat reference docs. Use when this capability is needed.
metadata:
  author: filmicgaze
---

## Purpose

Use this skill when the user is having trouble using MiChat itself (tools unavailable, settings not taking effect, confusion about where something is in the UI).

This skill is about **operating within MiChat (the current environment)**, not about the user’s external task domain.

## Authority order

1) If the active profile has a more specific relevant skill, follow that first.
2) Otherwise, use the global MiChat library docs:
   - **MiChat Operating Guide (for assistants)**
   - **MiChat Troubleshooting (for assistants)**

## How to consult the docs (practical)

Use library retrieval, not memory.

- If you need a fact but don’t know where it lives:
  - use `library_search(query)` with concrete queries (e.g. `"Working folder"`, `"Reset session now"`, `"Allow agent to edit its skills"`).
- Once you find the right area:
  - use `library_meta(path)` to get headings, then
  - use `library_read_section(path, heading_id)` to read the smallest relevant section.

Prefer reading one section and acting, rather than loading whole documents.

## Core posture

- Be evidence-led: use tool results and error envelopes; don’t guess.
- Ask one good question at a time.
- Don’t invent missing capabilities or toolsets.

## Triage loop (lightweight)

1) Clarify the symptom in one sentence: what were they trying to do?
2) If a tool call failed, read:
   - `error_type` (primary clue)
   - `retryable` (whether a retry might help)
3) Check MiChat’s common gates (most common first):
   - **Session reset banner** (settings changed but user didn’t click **Reset session now**)
   - **Working folder / `workspace_root`** (needed for documents/filesystem/open_file)
   - **Permission toggles** (e.g. web browsing; allow agent to edit its skills)
   - **Credentials** (Settings → Credentials)
4) Give one targeted UI instruction (with UI landmarks) and stop.
5) Re-attempt the minimal tool call to verify the fix (assistant-side) if the relevant tool exists.

## Known high-value checks

### Settings changes not taking effect

- Ask whether Settings shows the banner:
  - “Changes apply after resetting the agent session.”
  - Button: “Reset session now”.

### Documents/filesystem tools unavailable

- Guide user to: Settings → Paths → Working folder
- Remember: documents tools can read `.md/.txt/.json/.jsonl/.html/.htm`, but write `.md/.txt/.html/.htm`.

### Path errors

- Ensure the path is under the working folder and not using `..` traversal.

## When to hand off to the MiChat Assistant profile

If the user’s request is about **MiChat development or advanced configuration**, do not attempt to solve it in-place.

Examples:

- creating/modifying profiles
- adding new tools/toolsets/actions
- extending or changing the codebase
- editing global library content

Instead: instruct the user to switch to the **MiChat Assistant** profile and continue there.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filmicgaze) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
