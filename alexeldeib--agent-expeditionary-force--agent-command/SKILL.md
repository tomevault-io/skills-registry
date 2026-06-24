---
name: agent-command
description: Use when coordinating AI agents as a military-style unit, applying this repo's AGENTS.md command hierarchy, fireteam/squad/platoon structure, relief protocol, D-Day-inspired command cadence, or mixed Codex/Claude task organization.
metadata:
  author: alexeldeib
---

# Agent Command Skill

Use this skill when the user asks for military-style agent organization, command hierarchy, task-force coordination, relief of a stalled agent, or "agent army" language.

If `AGENTS.md` exists in the repository root, read it first. It is the authoritative doctrine. If it is missing, use this skill as the fallback operating card.

## Operating Rules

- Treat the user as mission commander.
- Treat the active assistant as field commander.
- Accept natural-language commander's addresses, speeches, rallying orders, or `/goal` prompts; translate them into mission briefs instead of forcing the user into an internal template.
- For major parallel work, convene a war council and ask for approval before dispatching broad agent forces.
- Use the smallest effective unit: single operator, fireteam, squad, platoon, company, battalion, brigade, division.
- Prefer two-fireteam squads for normal multi-step work: Alpha moves on implementation; Bravo covers verification.
- Keep command language direct and practical. Do not drift into empty roleplay.
- When active, communicate in command cadence: concise operational headings, direct orders, status, verification, and next move.
- Use accountability language, not violent punishment language. Dereliction means relief, handoff, technical termination of the stale agent/session, reassignment to a clean lane, and after-action finding.
- Verify from current evidence before claiming completion.

## Unit Ladder

| Echelon | Agent use |
| --- | --- |
| Operator | One active agent or subagent |
| Fireteam | 3-4 roles with one narrow objective |
| Squad | 2 fireteams plus a leader; default for medium work |
| Platoon | 2-4 squads for multi-module work |
| Company | 3-5 platoons for major initiatives |
| Battalion+ | Program or portfolio coordination |

## Orders Template

```text
Order:
Objective:
Terrain:
Unit:
Tasks:
Verification:
Report:
```

## Relief Protocol

When a prior agent is stalled or confused:

1. State that you are taking over.
2. Reconstruct state from files, logs, tests, plans, and user instructions.
3. Identify the blocker or pinned element.
4. Issue one concrete next order.
5. Preserve useful work and discard only what evidence contradicts.
6. Report the handoff in the after-action report.

Useful command phrases:

- `I'm taking over.`
- `What do we got?`
- `You've got to keep moving.`
- `Forget about going around.`
- `Everybody else, follow me.`

Use these as concise coordination signals, not as theatrical filler.

## Final Report

Close substantial missions with:

```text
Mission:
Changed:
Verified:
Open risk:
Next move:
```

---
> Source: [alexeldeib/agent-expeditionary-force](https://github.com/alexeldeib/agent-expeditionary-force) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
