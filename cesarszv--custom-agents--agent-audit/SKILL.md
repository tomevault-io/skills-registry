---
name: agent-audit
description: Use this skill to verify consistency and completeness across all three layers: Spanish staging, English production, and OpenCode deploy. Detects missing files, untranslated content, structural drift, and orphaned agents. Use when: 'audit agents', 'check agents', 'verify sync', or before committing changes.
metadata:
  author: cesarszv
---

# Agent Audit: Full Pipeline Verification

## Overview

This skill performs a comprehensive audit of the agent pipeline to detect inconsistencies, missing files, untranslated content, and structural drift across all three layers:

```
source/ + others/  (Spanish/Staging)
resources/languages/en/  (English/Production)
.opencode/agents/  (OpenCode/Deploy)
```

Run this skill **before committing** after any agent changes, or periodically to ensure the pipeline is healthy.

## The Audit Process

### Check 1: Agent Inventory Parity

**Goal:** Every agent must exist in all three layers.

**Procedure:**

1. Scan `source/` directories → list of `source` agents
2. Scan `others/` directories → list of `others` agents
3. Combine into **master agent list**
4. For each agent, verify existence in:
   - `resources/languages/en/source/<Agent>/AGENTS.md` (or `en/others/` for third-party)
   - `.opencode/agents/<agent-kebab>.md`

**Report format:**
```
AGENT INVENTORY PARITY
| Agent           | Spanish | English | OpenCode | Status |
|-----------------|---------|---------|----------|--------|
| Yupi Dupi       | OK      | OK      | OK       | PASS   |
| Carmen Marin    | OK      | MISSING | MISSING  | FAIL   |
```

**Failure actions:**
- Missing English → Run `agent-sync` skill
- Missing OpenCode → Run `agent-to-opencode` skill
- Missing Spanish → This is the source of truth; something was deleted or never created

### Check 2: File Structure Parity

**Goal:** Each agent must have the same set of files in Spanish and English.

**Procedure:**

For each agent:
1. List files in `source/<Agent>/` (or `others/<Agent>/`)
2. List files in `resources/languages/en/source/<Agent>/` (or `en/others/`)
3. Compare file lists

**Report format:**
```
FILE STRUCTURE PARITY
| Agent      | File       | Spanish | English | Status |
|------------|------------|---------|---------|--------|
| Yupi Dupi  | AGENTS.md  | OK      | OK      | PASS   |
| Yupi Dupi  | SOUL.md    | OK      | OK      | PASS   |
| Yupi Dupi  | README.md  | OK      | OK      | PASS   |
| Diana      | AGENTS.md  | OK      | OK      | PASS   |
| Diana      | README.md  | MISSING | MISSING | WARN   |
```

**Status meanings:**
- `PASS` - File exists in both languages
- `FAIL` - File exists in Spanish but missing in English (needs sync)
- `WARN` - File missing in both (not a sync issue, but maybe incomplete agent)
- `ORPHAN` - File exists in English but not in Spanish (shouldn't happen)

### Check 3: Section Structure Consistency

**Goal:** Translated files must have the same section structure as originals.

**Procedure:**

For each AGENTS.md / SOUL.md pair (Spanish vs English):
1. Extract all H2 headers (`## Section`) from the Spanish file
2. Extract all H2 headers from the English file
3. Map Spanish headers to expected English headers using the translation table
4. Report any mismatches

**Section header mapping:**
| Spanish | English |
|---------|---------|
| Personalidad | Personality |
| Idioma | Language |
| Tono | Tone |
| Filosofía | Philosophy |
| Experiencia | Experience |
| Comportamiento | Behavior |
| Reglas | Rules |
| Reglas Técnicas | Technical Rules |
| Objetivo | Objective |
| Instrucciones | Instructions |

**Report format:**
```
SECTION STRUCTURE
| Agent      | File      | Spanish Sections | English Sections | Status |
|------------|-----------|-----------------|-----------------|--------|
| Yupi Dupi  | SOUL.md   | 6               | 6               | PASS   |
| Carmen     | AGENTS.md | 8               | 8               | PASS   |
```

### Check 4: Content Freshness

**Goal:** Detect when Spanish source has been modified but English hasn't been updated.

**Procedure:**

For each file pair, compare:
1. Number of bullet points in each section
2. Number of lines in each section
3. If Spanish has significantly more content → English is stale

**Heuristic:** If a Spanish section has 2+ more lines than the English equivalent, flag it for review.

### Check 5: OpenCode Completeness

**Goal:** Every OpenCode agent file must have valid frontmatter and content.

**Procedure:**

For each file in `.opencode/agents/`:
1. Parse YAML frontmatter
2. Verify required fields exist:
   - [ ] `description` (non-empty, English)
   - [ ] `mode` (must be `subagent`)
   - [ ] `tools` (must have `write`, `edit`)
   - [ ] `permission.bash` (must be set)
   - [ ] `color` (valid hex or theme color)
3. Verify content body:
   - [ ] Has at least 3 H2 sections
   - [ ] No Spanish text detected (check for common Spanish words: "Personalidad", "Comportamiento", "Reglas", "Idioma", "Filosofía")
   - [ ] Content length > 500 characters

**Report format:**
```
OPENCODE VALIDATION
| File              | description | mode | tools | permission | color | content | Status |
|-------------------|-------------|------|-------|------------|-------|---------|--------|
| yupi-dupi.md      | OK          | OK   | OK    | OK         | OK    | OK      | PASS   |
| carmen-marin.md   | OK          | OK   | OK    | OK         | OK    | OK      | PASS   |
```

### Check 6: Orphan Detection

**Goal:** Detect files that exist in one layer but not others.

**Procedure:**

1. List all agent names from `.opencode/agents/` (reverse kebab-case to get display name)
2. Verify each has a corresponding Spanish source
3. Flag any OpenCode agent without a Spanish source as an orphan

Also check:
- English directories without Spanish counterparts
- Spanish directories not in the known locations (`source/` or `others/`)

## Full Audit Summary Template

```
===================================
AGENT PIPELINE AUDIT REPORT
Date: YYYY-MM-DD
===================================

LAYER COUNTS:
  Spanish (staging):  N agents
  English (production): N agents
  OpenCode (deploy):  N agents

CHECK 1 - INVENTORY PARITY:     PASS/FAIL (N issues)
CHECK 2 - FILE STRUCTURE:       PASS/FAIL (N issues)
CHECK 3 - SECTION STRUCTURE:    PASS/FAIL (N issues)
CHECK 4 - CONTENT FRESHNESS:    PASS/WARN (N stale)
CHECK 5 - OPENCODE VALIDATION:  PASS/FAIL (N issues)
CHECK 6 - ORPHAN DETECTION:     PASS/FAIL (N orphans)

OVERALL: PASS / NEEDS ATTENTION

RECOMMENDED ACTIONS:
  1. Run agent-sync for: <list>
  2. Run agent-to-opencode for: <list>
  3. Review stale translations: <list>
===================================
```

## When to Run

- **Before every commit** that touches agent files
- **After running agent-sync** to verify it worked
- **After running agent-to-opencode** to verify output
- **Periodically** (weekly recommended) to catch drift
- **When adding a new agent** to verify full pipeline coverage

## Key Principles

- **Non-destructive** - This skill only reads and reports; it never modifies files
- **Actionable output** - Every failure must include which skill to run to fix it
- **Complete coverage** - Checks all three layers, not just pairwise
- **Fast** - Only reads headers and metadata, not full content comparison (unless Check 4)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cesarszv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
