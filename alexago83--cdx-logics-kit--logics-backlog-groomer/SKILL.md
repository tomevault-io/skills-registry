---
name: logics-backlog-groomer
description: Groom and promote a Logics request into a backlog item. Use when a request is clear enough to define scope, acceptance criteria, and priority, and Codex should create `logics/backlog/*.md` aligned with the Logics format. Use when this capability is needed.
metadata:
  author: alexago83
---

# Request → Backlog

Examples use `python ...` as the canonical cross-platform launcher.
If your environment only exposes `python3` or `py -3`, substitute that launcher.

## Do

- Read the source request and extract: problem, users impacted, constraints, and success signal.
- Define `# Scope` (In/Out) to reduce ambiguity.
- Write objective `# Acceptance criteria` (testable checks).
- Set `# Priority` (Impact/Urgency) and add dependencies/risks in `# Notes`.
- Create the backlog doc:
  - `python logics/skills/logics.py flow promote request-to-backlog logics/request/<req_file>.md`
  - Or `python logics/skills/logics.py flow new backlog --title "..."`

## Outcome

A backlog item is “ready” when an engineer could implement it without guessing the intended behavior.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
