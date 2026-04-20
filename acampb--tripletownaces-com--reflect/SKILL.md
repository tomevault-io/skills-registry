---
name: reflect
description: > Use when this capability is needed.
metadata:
  author: acampb
---

# Reflect

Capture learnings and mutate skills directly with new wisdom.

## Sub-Commands

| Command             | Purpose                                                      |
| ------------------- | ------------------------------------------------------------ |
| `/reflect capture`  | Quick save observations to pending file (run frequently)     |
| `/reflect finalize` | Process pending into skill updates + log to index.md         |
| `/reflect migrate`  | Move legacy principles from index.md into appropriate skills |

## Before Any Sub-Command

1. **Check pending file** - Read `.claude/reflections/.pending.md` if exists
2. **Know the skills** - `ls .claude/skills/` to see what exists

---

## `/reflect capture`

### When to Use

- After solving a tricky problem
- When you notice a pattern emerging
- Before conversation gets too long (anticipate compaction)
- Periodically during long sessions

### Steps

1. **Identify observations** from recent work:
   - Patterns that emerged
   - Friction points encountered
   - Decisions made and rationale
   - Things learned

2. **Append to pending file** (`.claude/reflections/.pending.md`):

```markdown
## Capture: {timestamp}

### Context

{What was being worked on}

### Observations

- {Pattern or learning 1}
- {Pattern or learning 2}

### Potential Target Skills

- {skill-name}: {Brief idea why}
```

3. **Confirm saved** - Tell user observations captured

No approval needed - this is just accumulation.

---

## `/reflect finalize`

### When to Use

- End of work session
- After completing significant feature
- When ready to formalize learnings

### Phase 1: Gather All Learnings

1. **Read pending file** - `.claude/reflections/.pending.md`
2. **Scan conversation** - Identify anything not yet captured
3. **List every observation** - Extract each distinct observation as a separate line item
4. **Group related items** - Visually cluster similar observations (but don't merge yet)

**Do NOT merge yet** - consolidation happens after scoring to preserve fidelity.

### Phase 2: Score Each Observation Individually

**CRITICAL**: Score each observation from the pending file individually before any merging. Premature consolidation loses valuable specificity.

For each observation, evaluate against the timelessness rubric:

| Criterion       | Score | Reasoning                      |
| --------------- | ----- | ------------------------------ |
| Generalizable   | X/10  | [Applies beyond this task?]    |
| Durable         | X/10  | [Relevant in 6 months?]        |
| Specific        | X/10  | [Actionable without guessing?] |
| **Overall**     | X/10  | **[Include/Exclude/Revise]**   |

Then present the summary table:

| #   | Observation               | G   | D   | S   | Avg | Decision               | Reasoning            |
| --- | ------------------------- | --- | --- | --- | --- | ---------------------- | -------------------- |
| 1   | {exact text from pending} | X   | X   | X   | X   | Include/Exclude/Revise | {brief rationale}    |
| 2   | {exact text from pending} | X   | X   | X   | X   | Include/Exclude/Revise | {brief rationale}    |

**Thresholds:**

- 1-4: Exclude - too specific or transient
- 5-6: Revise - make more general/durable
- 7-10: Include - good candidate for skill update

### Phase 2b: Merge Only After Scoring

After scoring, identify observations that express the **same underlying principle**:

- Only merge if combining does NOT lose actionable detail
- If in doubt, keep separate
- Show merge reasoning: "Merging #3 and #7 - both express X"

### Phase 3: Identify Target Skills

For each included observation:

1. **Read ALL skill descriptions** - `head -20 .claude/skills/*/SKILL.md` to get frontmatter
2. **Match observation to skill** based on:
   - Does the skill's domain cover this learning?
   - Would this improve the skill's guidance?
   - Is there a specific section this fits into?

Present the mapping:

| Observation | Target Skill | Section       | Reasoning                            |
| ----------- | ------------ | ------------- | ------------------------------------ |
| {obs 1}     | finalize     | Bug Check     | Improves edge case coverage          |
| {obs 2}     | skill-builder| Design section| Clarifies pattern selection          |
| {obs 3}     | CLAUDE.md    | Conventions   | Project-wide rule                    |
| {obs 4}     | NEW: {name}  | -             | No existing skill covers this domain |

**If observation targets a skill, investigate that skill deeply:**

- Read the full SKILL.md
- Understand its current structure
- Identify exact insertion point

### Phase 4: Draft Skill Updates

Present ALL proposed updates:

```
Reflection Summary
==================

Session: {Brief description of work done}

Proposed Updates
----------------

1. UPDATE: {skill-name} skill
   File: .claude/skills/{skill-name}/SKILL.md
   Section: {section name}

   Current:
   {existing text or "New section"}

   Proposed:
   {new text}

   Reasoning: {Why this improves the skill}

   Approve? [Yes/No/Modify]

---

2. ADD TO: CLAUDE.md
   Section: {Conventions / Commands / etc.}

   Proposed:
   {new text}

   Approve? [Yes/No/Modify]

---

Reflection Log
--------------
Will save to: .claude/reflections/{date}-{title}.md
Will update: .claude/reflections/index.md (log table only)

Approve? [Yes/No/Skip]
```

### Phase 5: Apply Approved Changes

For each approved update:

1. **Edit the target skill file directly**
2. Verify the edit is correct
3. Mark as applied

After all skill updates:

- Clear `.claude/reflections/.pending.md`
- Write reflection log to `.claude/reflections/{date}-{title}.md`
- **Update index.md log table only** (not principles sections)

### What Goes Where

| Content Type            | Destination                           |
| ----------------------- | ------------------------------------- |
| Skill-specific pattern  | That skill's SKILL.md                 |
| Project-wide convention | CLAUDE.md                             |
| New capability          | New skill via `/skill-builder create` |
| Session history         | Reflection log + index.md table       |

**index.md is now an audit log**, not a wisdom repository. Wisdom lives in skills.

---

## `/reflect migrate`

### When to Use

- Transitioning from old mental model (wisdom in index.md) to new (wisdom in skills)
- Periodic cleanup of legacy principles
- When index.md has principles that should live in skills

### Steps

1. **Read index.md** - `.claude/reflections/index.md`

2. **Read ALL skill descriptions** - `head -20 .claude/skills/*/SKILL.md`

3. **For each principle in index.md**, determine:

   | Principle        | Target       | Action                |
   | ---------------- | ------------ | --------------------- |
   | {principle text} | {skill-name} | Move to {section}     |
   | {principle text} | CLAUDE.md    | Move to Conventions   |
   | {principle text} | None         | Delete (too specific) |
   | {principle text} | Keep         | Truly cross-cutting   |

4. **For skills identified as targets, read fully** to find exact insertion point

5. **Present migration plan:**

   ```
   Migration Plan
   ==============

   Moving to skills:
   -----------------
   1. "Skill descriptions are the trigger mechanism..."
      → skill-builder: Add to "Before Any Sub-Command" or design section

   2. "Reference file sizing: <100 lines..."
      → skill-builder: Add to sizing rules

   Moving to CLAUDE.md:
   --------------------
   3. "Always use strict mode..."
      → CLAUDE.md: Conventions section

   Keeping in index.md:
   --------------------
   4. "Context rot - as context windows expand..."
      → Truly cross-cutting, no single skill owns this

   Deleting:
   ---------
   5. "Use the specific-project-migration..."
      → Too specific to one task

   Approve? [Yes/No/Modify]
   ```

6. **Apply approved migrations:**
   - Edit target skills
   - Remove migrated items from index.md
   - Keep index.md structure (sections remain for future cross-cutting principles)

---

## File Formats

### Pending File

Location: `.claude/reflections/.pending.md`

```markdown
# Pending Reflections

Captured observations awaiting finalization.

---

## Capture: 2026-01-08 14:30

### Context

Working on feature implementation

### Observations

- Pattern X works well for Y scenarios
- Approach Z had unexpected friction

### Potential Target Skills

- finalize: Add edge case to checklist
- skill-builder: Clarify when to use sub-commands
```

### Reflection Log

Location: `.claude/reflections/{date}-{title}.md`

```markdown
# Reflection: {Brief Title}

## Date

{YYYY-MM-DD}

## Context

{What task was being worked on}

## What Worked

- {Effective pattern or approach}

## What Didn't Work

- {Initial approach that failed}

## Skills Updated

- [x] {skill}: {change made}
- [x] CLAUDE.md: {change made}
- [ ] {skill}: {declined or deferred}

## Principles Extracted

- {High-level principle that was added to a skill}
```

### index.md Structure

Location: `.claude/reflections/index.md`

```markdown
# Reflections Index

Session history and cross-cutting principles.

## Cross-Cutting Principles

Principles that don't belong to any single skill:

- {Truly general principle}

---

## Reflection Log

| Date       | Title             | Skills Updated         |
| ---------- | ----------------- | ---------------------- |
| 2026-01-08 | Feature Patterns  | finalize, skill-builder|
| 2026-01-13 | Skill Research    | skill-builder, reflect |
```

---

## Checklist

### For `/reflect capture`:

- [ ] Identified observations from recent work
- [ ] Appended to `.claude/reflections/.pending.md`
- [ ] Noted potential target skills
- [ ] Confirmed save to user

### For `/reflect finalize`:

- [ ] Read pending file and conversation
- [ ] Scored each learning for timelessness
- [ ] Read all skill descriptions to identify targets
- [ ] Read target skills fully to find insertion points
- [ ] Drafted skill updates with exact sections
- [ ] Checked CLAUDE.md for project-wide conventions
- [ ] Presented all changes for approval
- [ ] Applied only approved changes to skills
- [ ] Cleared pending file
- [ ] Saved reflection log
- [ ] Updated index.md log table (not principles)

### For `/reflect migrate`:

- [ ] Read index.md principles
- [ ] Read all skill descriptions
- [ ] Mapped each principle to target
- [ ] Read target skills fully
- [ ] Presented migration plan
- [ ] Applied approved migrations
- [ ] Removed migrated items from index.md

---

## Reference Files

- Pending: `.claude/reflections/.pending.md`
- Index: `.claude/reflections/index.md` (log table + cross-cutting only)
- Skills: `.claude/skills/*/SKILL.md` (where wisdom lives)
- Conventions: `CLAUDE.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/acampb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
