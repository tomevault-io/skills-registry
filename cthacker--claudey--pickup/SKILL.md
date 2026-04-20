---
name: pickup
description: Rehydrate context from HANDOFF.md when starting a new session. Use when resuming previous work or picking up where another agent left off. Use when this capability is needed.
metadata:
  author: cthacker
---

# Pickup

Rehydrate context quickly when you start work.

## Steps

1. **Read handoff and project docs**
   - Read `HANDOFF.md` in the project root. If it doesn't exist, tell the user and ask how to proceed.
   - Read `CLAUDE.md` / `AGENTS.md` if present for project conventions.

2. **Repo state**
   - Run `git status -sb` and check for unpushed local commits.
   - Confirm current branch matches what the handoff documents.
   - Note any discrepancies between the handoff and actual state.

3. **CI / PR**
   - If a PR is noted in the handoff, run `gh pr view <number> --comments --files` and note failing checks.
   - If no PR number, try to derive one from the current branch with `gh pr view`.

4. **Tests / checks**
   - Note what last ran (from handoff notes or CI) and what still needs to run.

5. **Present summary and plan**
   - Summarize: what was being worked on, what's done, what's pending, any discrepancies found.
   - Plan the next 2-3 actions as bullets.
   - Ask the user which action to start (or if they want to do something different).

6. **Execute** — once the user picks, begin work.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cthacker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
