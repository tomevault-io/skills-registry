---
name: specs
description: Deep scan of specs directory — shows status table, staleness, progress summary, and optionally verifies code implementations Use when this capability is needed.
metadata:
  author: jerichobob
---

# Specs Status

Deep scan of the specs directory with staleness detection and README validation.

## Pre-parsed Data

### Spec Status (TSV: version, name, done, total, status)

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh status`

### Staleness Check

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh staleness`

## Instructions

### 1. Display Status Table

Using the status data above, display a summary table:

```text
| Version | Name                    | Progress | Status         |
|---------|-------------------------|----------|----------------|
```

Use these status icons:

- `Complete` → `✅ Complete`
- `In Progress` → `🔄 In Progress`
- `Draft` → `📝 Draft`
- `Empty` → `⬜ Empty`

Show total tasks completed vs total tasks as a summary line.

### 2. Staleness Report

Using the staleness data above, report:

- If STALE_FILES shows `(none)`: README is current
- Otherwise: list which spec files are newer than README — potential drift

### 3. Auto-Update README (if needed)

If the Quick Status table in `specs/README.md` has incorrect progress numbers:

1. Show what's wrong (expected vs actual)
2. Update the table with correct counts
3. Report that README was updated

### 4. Code Verification (only if --verify argument provided)

When `$ARGUMENTS` contains `--verify` or `verify`:

For EVERY `[x]` (completed) and `[ ]` (pending) task in the README:

1. Parse the task description to identify verifiable artifacts
2. Search codebase for evidence (files, functions, classes, patterns)
3. Categorize findings:
   - `[x]` marked done AND found in code → correct
   - `[x]` marked done but NOT found → false positive (README ahead of code)
   - `[ ]` marked pending but FOUND in code → needs checkbox update (README behind code)
   - `[ ]` marked pending and not found → correctly pending

For complete specs: spot-check key artifacts.
For in-progress/draft specs: verify all items thoroughly.

## Output Format

```text
📊 {Project Name} - Specs Overview

| Version | Name                    | Progress | Status         |
|---------|-------------------------|----------|----------------|
| v1      | MVP Demo                | 8/8      | ✅ Complete     |
| v2      | Feature X               | 5/10     | 🔄 In Progress |

---
Summary: N/M tasks complete (X%)

Staleness: ✅ README.md is current
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jerichobob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
