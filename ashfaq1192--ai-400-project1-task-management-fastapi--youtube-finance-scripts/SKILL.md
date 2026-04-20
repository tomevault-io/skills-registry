---
name: youtube-finance-scripts
description: | Use when this capability is needed.
metadata:
  author: ashfaq1192
---

# YouTube Finance Script Generator

Generate engaging 8-10 minute scripts for personal finance explainer videos focused on money mindset and behavior.

## Before Implementation

| Source | Gather |
|--------|--------|
| **Conversation** | Video topic, specific angle, key points to cover |
| **Skill References** | Hook formulas, retention patterns, script structure |
| **User Guidelines** | Channel-specific phrases, recurring segments, brand elements |

## Quick Start

**Input needed:**
1. Topic (e.g., "Why most people stay broke")
2. Angle/unique take (what makes THIS video different)
3. 3-5 key points to cover
4. Desired viewer transformation (what they'll believe/do after)

**Output delivered:**
1. 3 hook options (pick one)
2. Full timestamped script (~1,500-2,000 words)
3. B-roll and visual suggestions
4. Thumbnail title options

---

## Script Generation Workflow

### Phase 1: Angle Development

Before writing, define:

```
TOPIC: [Main subject]
ANGLE: [Your unique take - why watch THIS video?]
VIEWER BEFORE: [Current belief/state]
VIEWER AFTER: [Desired belief/state]
CORE MESSAGE: [One sentence they'll remember]
```

**Angle types that work:**
- Contrarian: "Why [common advice] is wrong"
- Hidden truth: "What [experts] won't tell you about..."
- Framework: "The [X] method for [result]"
- Story: "How I [achieved result] by [unexpected method]"
- Myth-busting: "[Number] lies you've been told about..."

### Phase 2: Hook Selection

Generate 3 hooks using formulas from `references/hook_formulas.md`:

| Hook Type | Best For |
|-----------|----------|
| **Contrarian** | Challenging common beliefs |
| **Story** | Personal experience topics |
| **Problem-Agitate** | Pain point topics |
| **Curiosity Gap** | "Secret" or "hidden" knowledge |
| **Stakes** | Consequences of inaction |

**Hook structure (0-30 seconds):**
```
[Pattern interrupt - unexpected statement]
[Stakes - why this matters NOW]
[Promise - what they'll learn]
[Credibility hint - why listen to you]
```

### Phase 3: Script Architecture

Use the 8-10 minute structure:

```
0:00-0:30  │ HOOK (Critical - determines if they stay)
0:30-1:30  │ CONTEXT + PROMISE (Setup the problem, preview solution)
1:30-3:30  │ SECTION 1 (First key point + example)
3:30-4:00  │ PATTERN INTERRUPT (Re-engage attention)
4:00-5:30  │ SECTION 2 (Second key point + example)
5:30-6:00  │ PATTERN INTERRUPT
6:00-7:30  │ SECTION 3 (Third key point + example)
7:30-8:30  │ SYNTHESIS (Bring it together, the "aha")
8:30-9:30  │ CTA + TEASE (Action step + next video hint)
```

See `references/script_structure.md` for detailed breakdown.

### Phase 4: Writing the Script

**Voice guidelines (Educational & Trustworthy):**
- Speak TO viewer, not AT them ("You've probably noticed..." not "People often...")
- Use "we" for shared experiences ("We've all been told...")
- Short sentences. Punchy. Easy to deliver.
- Include pauses: [beat], [pause for emphasis]
- Data + story = credibility + relatability

**Retention techniques (every 60-90 seconds):**
- Open loops: "I'll show you exactly how in a minute, but first..."
- Pattern interrupts: Energy shift, B-roll, direct question
- Resets: "Now here's where it gets interesting..."
- Stakes reminders: "And this is why it matters..."

See `references/retention_patterns.md` for full list.

**Section formula:**
```
SETUP: "Here's what most people believe about [X]..."
TENSION: "But there's a problem with that..."
INSIGHT: "What actually works is..."
PROOF: "For example..." or "Studies show..." or "When I tried this..."
TRANSITION: Open loop to next section
```

---

## Script Output Format

```markdown
# [VIDEO TITLE]

## Metadata
- **Topic**:
- **Angle**:
- **Target length**: 8-10 minutes (~1,800 words)
- **Viewer transformation**: [Before] → [After]

---

## HOOK OPTIONS (Pick One)

### Option A: [Type]
[Full hook script]

### Option B: [Type]
[Full hook script]

### Option C: [Type]
[Full hook script]

---

## FULL SCRIPT

### [0:00-0:30] HOOK
[Script with delivery notes]

### [0:30-1:30] CONTEXT + PROMISE
[Script]
[B-ROLL: suggestion]

### [1:30-3:30] SECTION 1: [Title]
[Script]
[PATTERN INTERRUPT at 3:00]

### [3:30-4:00] TRANSITION
[Pattern interrupt / re-engagement]

### [4:00-5:30] SECTION 2: [Title]
[Script]

### [5:30-6:00] TRANSITION
[Pattern interrupt]

### [6:00-7:30] SECTION 3: [Title]
[Script]

### [7:30-8:30] SYNTHESIS
[Bring it all together]

### [8:30-9:30] CTA + TEASE
[Call to action]
[Tease next video]

---

## THUMBNAIL + TITLE OPTIONS

1. [Title option 1]
2. [Title option 2]
3. [Title option 3]

**Thumbnail concept**: [Visual suggestion]

---

## B-ROLL SUGGESTIONS

| Timestamp | Visual |
|-----------|--------|
| 0:45 | [suggestion] |
| ... | ... |
```

---

## Topic Categories & Angles

### Money Mindset Topics
- Scarcity vs abundance thinking
- Relationship with money (childhood programming)
- Fear of wealth / money blocks
- Lifestyle inflation psychology
- Delayed gratification
- Identity and net worth

### Behavioral Finance Topics
- Why we overspend (dopamine, social proof)
- Automation psychology
- Decision fatigue and money
- Sunk cost fallacy
- Mental accounting
- Present bias

### Financial Freedom Topics
- Defining "enough"
- Time vs money tradeoffs
- The hedonic treadmill
- Lifestyle design principles
- Coast FIRE, Barista FIRE concepts
- Work optional mindset

---

## Quality Checklist

Before delivering script:

- [ ] Hook creates curiosity in first 5 seconds
- [ ] Promise is clear by 0:30
- [ ] Each section has: setup → tension → insight → proof
- [ ] Pattern interrupt every 60-90 seconds
- [ ] At least 2 open loops used
- [ ] Specific examples (numbers, stories, studies)
- [ ] Script reads naturally out loud
- [ ] Clear single CTA at end
- [ ] Viewer transformation is achieved
- [ ] ~1,500-2,000 words (8-10 min at speaking pace)

---

## Resources

- [hook_formulas.md](references/hook_formulas.md) - 15+ proven hook templates
- [retention_patterns.md](references/retention_patterns.md) - Pattern interrupts & open loops
- [script_structure.md](references/script_structure.md) - Minute-by-minute breakdown
- [example_script.md](assets/example_script.md) - Full example script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashfaq1192) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
