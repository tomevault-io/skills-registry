---
name: code-analyzer
description: Static code analysis and complexity metrics Use when this capability is needed.
metadata:
  author: neversight
---

# Code Analyzer Skill

## Overview

Static code analysis and metrics. 90%+ context savings.

## Tools (Progressive Disclosure)

### Analysis

| Tool            | Description                  |
| --------------- | ---------------------------- |
| analyze-file    | Analyze single file          |
| analyze-project | Analyze entire project       |
| complexity      | Calculate complexity metrics |

### Metrics

| Tool            | Description           |
| --------------- | --------------------- |
| loc             | Lines of code         |
| cyclomatic      | Cyclomatic complexity |
| maintainability | Maintainability index |
| duplicates      | Find duplicate code   |

### Reporting

| Tool     | Description              |
| -------- | ------------------------ |
| summary  | Get analysis summary     |
| hotspots | Find complexity hotspots |
| trends   | Analyze metric trends    |

## Agent Integration

- **code-reviewer** (primary): Code review
- **refactoring-specialist** (primary): Tech debt analysis
- **architect** (secondary): Architecture assessment

## Memory Protocol (MANDATORY)

**Before starting:**
Read `.claude/context/memory/learnings.md`

**After completing:**

- New pattern -> `.claude/context/memory/learnings.md`
- Issue found -> `.claude/context/memory/issues.md`
- Decision made -> `.claude/context/memory/decisions.md`

> ASSUME INTERRUPTION: If it's not in memory, it didn't happen.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
