---
name: dragon-dev-loop
description: > Use when this capability is needed.
metadata:
  author: united-digiart-vision
---

# Dragon Dev Loop

Standard development process. **No exceptions for any code change delivered to Dino.**

**Qualität ist die Priorität. IMMER. Keine Abkürzungen.**

## The Loop

```
1. REQUIREMENTS (Syrax 📐)      → PRD / REQUIREMENTS.md — ALLE Anforderungen (alte + neue), jede mit ID, jede testbar
2. TESTS FIRST (Vermithrax ⚖️)  → Test spec + acceptance criteria — JEDE Requirement hat mindestens einen Test
3. IMPLEMENT (Caraxes 🔥)       → Code against tests — nicht vorher, nicht ohne
4. QA (Vermithrax ⚖️)           → Test JEDE Requirement, report, PASS/FAIL pro Requirement
5. FAIL? → Back to 3            → Loop until ALL requirements PASS
6. PASS → Deliver to Dino       → With FULL protocol in chat
```

## Roles

| Dragon | Role | When |
|--------|------|------|
| **Balerion** 🐉 | Orchestration, final validation, delivery to Dino | Start + End |
| **Syrax** 📐 | Requirements (PRD), Design, Datenmodelle | Step 1 — BEFORE any code |
| **Vermithrax** ⚖️ | Define test specs, run QA, PASS/FAIL | Steps 2 + 4 |
| **Caraxes** 🔥 | Implement, fix findings | Step 3 |

## ⛔ HARD RULES — KEINE AUSNAHMEN

1. **Syrax ZUERST** — Kein Code ohne Requirements-Dokument. Syrax dokumentiert ALLE Anforderungen (bestehende + neue) als PRD/REQUIREMENTS.md
2. **Vermithrax vor Caraxes** — Kein Code ohne Test-Spec. Vermithrax definiert Tests BEVOR Caraxes implementiert
3. **Caraxes implementiert gegen Tests** — Nicht freestyle, nicht "ich mach mal"
4. **Vermithrax testet ALLES** — Nicht nur neue Features, ALLE Requirements der aktuellen Phase
5. **Kein FAIL an Dino** — Loop bis PASS. Wenn Loop > 3x → Report an Dino mit Analyse
6. **IMMER volles Protokoll** bei Lieferung

## Delivery Protocol (MANDATORY)

Every delivery to Dino includes IN THE CHAT:
1. Requirements document (path + key requirements)
2. Test specification (path + what/how tested)
3. Traceability matrix (requirement → test → result)
4. All file paths changed
5. Pass/fail decision with numbers (X/Y tests passed)

**No "it's done, check it out"!** Always full protocol.

## Escalation

- Loop > 3x without PASS → Report to Dino with analysis
- Unclear requirements → Ask Dino BEFORE starting
- Technically impossible → Report with alternatives

## Continuous Learning (MANDATORY)

**BEFORE starting any Dev Loop:** Read `dragons/dev-loop-learnings.md`
- Contains rules derived from REAL mistakes
- Every agent reads this before working
- After the loop: Balerion updates it with new learnings

**AFTER every Dev Loop:** Balerion asks:
1. Were requirements clear enough? (Syrax quality)
2. Did tests catch real bugs? (Vermithrax quality)
3. Did Caraxes need to ask questions? (Handoff quality)
4. Did Dino find issues we missed? (Coverage gap)
5. What rule do we derive from this?

Update `dragons/dev-loop-learnings.md` with the answers.

## Agent Playbooks

Each dragon has a SOUL.md with detailed personality, rules, and do's/don'ts:
- `dragons/syrax/SOUL.md` — Requirements Lead
- `dragons/vermithrax/SOUL.md` — QA Executor
- `dragons/caraxes/SOUL.md` — Lead Engineer
- `dragons/WORKFLOWS.md` — Handoff protocol between dragons

Include relevant SOUL.md path in each spawn task so the agent reads it.

## Why This Exists

Dino's Worte: "Qualität ist die Priorität. Ich will es nicht noch mal erklären."

Jede Abkürzung kostet mehr als sie spart. Der Loop ist nicht optional. Er ist die Grundlage unserer Arbeit.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/united-digiart-vision) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
