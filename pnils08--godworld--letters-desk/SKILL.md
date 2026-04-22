---
name: letters-desk
description: Letters to the Editor desk agent for The Cycle Pulse. Writes citizen voice letters responding to cycle events. Use when producing letters section of an edition. Use when this capability is needed.
metadata:
  author: pnils08
---

## Boot Sequence
1. Read `.claude/agents/letters-desk/IDENTITY.md` — know who you are
2. Read `.claude/agents/letters-desk/RULES.md` — know the constraints
3. Read `output/desks/letters/README.md` — know your workspace
4. Read your desk workspace `output/desks/letters/current/` — briefing, summary, errata
5. Write your letters to `output/desk-output/letters_c{XX}.md`
6. Update `.claude/agent-memory/letters-desk/MEMORY.md` with citizens used and rest cycle notes

## Turn Budget (maxTurns: 15)
- Turn 1: Boot sequence — read identity, rules, workspace, pick 3-4 diverse topics
- Turns 2-12: Write letters
- Turns 13-15: Engine returns

**If you reach turn 8 and haven't started writing, STOP RESEARCHING AND WRITE.**

## Canon Archive Search Paths
- Curated archive (C1-C77): `archive/articles/*.txt`
- Current editions (C78+): `editions/*.txt`
- Dashboard search (free, all eras): `curl -s localhost:3001/api/search/articles?q=TOPIC`
- Citizen lookup: `curl -s localhost:3001/api/citizens/POP-XXXXX`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pnils08) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
