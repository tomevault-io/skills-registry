---
name: prose
description: | Use when this capability is needed.
metadata:
  author: qinolabs
---

# Prose Skill

Chronicle writing, research transmission, and prose transformation. Natural language activation — users describe intent, Claude routes to the appropriate workflow.

---

## Routing

Match user intent to workflow. Read the workflow file and follow its instructions.

| User Intent | Workflow |
|-------------|----------|
| Write chronicle chapter, "next chapter" | [workflows/chapter.md](workflows/chapter.md) |
| Survey git history, "plan chapters" | [workflows/survey.md](workflows/survey.md) |
| Diagnose scribe system, "check scribe" | [workflows/diagnose.md](workflows/diagnose.md) |
| Rewind last chapter, "undo chapter" | [workflows/rewind.md](workflows/rewind.md) |
| Create visual style, "configure images" | [workflows/visual-style.md](workflows/visual-style.md) |
| Transmit research, "create transmission" | [workflows/transmit.md](workflows/transmit.md) |
| Apply narrator lens | [workflows/narrator.md](workflows/narrator.md) |
| Apply wanderer lens | [workflows/wanderer.md](workflows/wanderer.md) |

---

## Three Domains

### Chronicle (from qino-scribe)

Transform git history into narrative. Multi-agent architecture with prep, prose, and editorial stages.

**Workflows:** chapter, survey, diagnose, rewind, visual-style

**Agents:**
- `agents/scribe-prep.md` — Lens-first prep with checkpoints
- `agents/scribe-prose.md` — Fantasy author writing from constraint
- `agents/scribe-editorial.md` — Voice review and revision

**References:**
- `references/chronicle/voice.md` — Craft devices and patterns
- `references/chronicle/craft.md` — Chapter format, world structure
- `references/chronicle/layers.md` — Layer architecture
- `references/chronicle/story-lenses.md` — Twelve lenses
- `references/chronicle/disturbance.md` — Git diff interpretation
- `references/chronicle/principles.md` — Relational substrate
- `references/chronicle/foundation.md` — World-seed configuration

### Transmission (from qino-relay)

Voice research arcs through the Student as reader companion.

**Workflows:** transmit

**Agents:**
- `agents/relay-prose.md` — Student voice
- `agents/relay-editorial.md` — Voice integrity review

**References:**
- `references/transmission/voice.md` — Student voice patterns
- `references/transmission/student-guide.md` — How the Student works
- `references/transmission/transmission-format.md` — Format specification
- `references/transmission/reader-journey-guide.md` — Reader journey prep

### Lenses (from qino-lens)

Transform prose through specific qualities of attention.

**Workflows:** narrator, wanderer

**References:**
- `references/lenses/narrator.md` — World as participant, body first
- `references/lenses/wanderer.md` — Embodied presence, contextual density
- `references/lenses/companion.md` — Companionship lens
- `references/lenses/newcomer.md` — Newcomer lens
- `references/lenses/student.md` — Student lens

---

## Context Detection

Before routing, check for chronicle or journal context:

1. **Chronicle context:** Look for `chronicle/manifest.json`
2. **Journal context:** Look for `journal/manifest.json` or via `.claude/qino-config.json`

This determines which workflows are available and where they operate.

---

## Error States

**No chronicle for chapter/survey/rewind:**
> "No chronicle found. Create one with: write a chronicle chapter"

**No journal for transmit:**
> "No journal found. Set up research workspace first."

**Unknown intent:**
> Route to `workflows/chapter.md` if chronicle exists, otherwise explain available workflows.

---

## Agent Reference

Agents are organized by domain:

**Chronicle agents:**
- `agents/scribe-prep.md`
- `agents/scribe-prose.md`
- `agents/scribe-editorial.md`

**Transmission agents:**
- `agents/relay-prose.md`
- `agents/relay-editorial.md`

**Lens workflows are direct transformations — no agents needed.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qinolabs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
