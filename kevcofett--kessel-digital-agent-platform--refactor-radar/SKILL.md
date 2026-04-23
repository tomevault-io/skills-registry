---
name: refactor-radar
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Refactor Radar

## Commands

- `/refactor <path>` — Scan a file or directory for refactoring opportunities
- `/refactor --hotspots` — Find the most complex files in the project
- `/refactor --duplicates` — Find code duplication across the codebase

## Code Smells Detected

### Structural

- Long methods (50+ lines)
- God classes (300+ lines, 10+ methods)
- Deep nesting (3+ levels of if/for/while)
- Long parameter lists (5+ parameters)
- Feature envy (method uses another class's data more than its own)

### Duplication

- Copy-paste blocks (3+ identical or near-identical blocks)
- Similar logic in different functions that should be unified
- Repeated patterns that could be abstracted

### Complexity

- Cyclomatic complexity above 10
- Boolean logic with 3+ conditions
- Switch/match statements with 7+ cases
- Callback hell or deeply nested promises

### Naming

- Single-letter variables outside loop counters
- Misleading names (variable does something different than name suggests)
- Inconsistent naming conventions within a file

## Procedure

### Phase 1: Scan

1. Read the target files
2. Calculate metrics (line count, complexity, nesting depth)
3. Identify code smells from the checklist above

### Phase 2: Prioritize

Rate each finding by:

- Impact: How much does this hurt readability/maintainability? (HIGH/MEDIUM/LOW)
- Effort: How hard is the refactor? (HIGH/MEDIUM/LOW)
- Risk: Could the refactor break things? (HIGH/MEDIUM/LOW)

Priority = High Impact + Low Effort + Low Risk first.

### Phase 3: Report

For each finding:

- Location (file:line range)
- Smell type
- Current state (brief description)
- Suggested refactor (concrete approach)
- Impact/effort/risk assessment

### Phase 4: Refactor (if requested)

Only refactor when the user explicitly asks. Never auto-refactor.

## MCMAP-Specific Patterns

- Python scripts in scripts/ — check for config duplication across files
- Cloud Flow JSON — check for duplicated action patterns across workflows
- Copilot instructions — check for inconsistent formatting across agents

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
