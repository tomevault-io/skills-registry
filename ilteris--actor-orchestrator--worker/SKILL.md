---
name: worker
description: Distributed Engineer with mandatory Git/PR lifecycle. Use when this capability is needed.
metadata:
  author: ilteris
---

# SKILL: Worker (The Atomic Contributor)

## Operational Protocol
...
5. **GIT SUBMIT**:
   - `git push origin task-<ID>-<slug>`
   - **MANDATORY TOOL CALL**: `gh pr create ...`
   - **MANDATORY LOG OUTPUT**: You MUST print the URL of the created PR exactly as follows:
     `PULL_REQUEST_URL: <URL>`
...

## Constraints
- **FAILURE MODE**: If you finish a task without creating a PR, the task is incomplete.
- **DASHBOARD HOOK**: The system events scraper depends on the `PULL_REQUEST_URL: ` string.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ilteris) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
