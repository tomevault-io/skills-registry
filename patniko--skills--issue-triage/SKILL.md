---
name: issue-triage
description: Fetch GitHub issues and use LLM judgment to prioritize them based on importance, clarity, delegation potential, and urgency. Helps identify what to work on next. Use when this capability is needed.
metadata:
  author: patniko
---

# Issue Triage

Fetch GitHub issues, apply intelligent analysis, and visualize priorities.

## When to Use

- Starting a work session and need to decide what to tackle
- Triaging a backlog with many competing priorities
- Looking for issues that can be delegated to AI coding agents
- Identifying urgent vs. important vs. quick-win issues

## Quick Start

```bash
# 1. Fetch issues (saves to triage-data.json)
./scripts/fetch-issues.sh owner/repo

# 2. Launch dashboard in browser
./scripts/serve.sh
```

## How This Skill Works

This skill combines **deterministic data fetching** with **LLM judgment**:

1. **Script fetches issues** → outputs structured `triage-data.json`
2. **You analyze each issue** using the scoring criteria below
3. **Update the JSON** with scores and analysis
4. **Use the viewer** to sort, filter, and explore prioritized issues

The script handles data retrieval; you provide the intelligence that only an LLM can offer.

## Data Format

The fetch script outputs JSON in this structure:

```json
{
  "metadata": {
    "repository": "owner/repo",
    "generated": "2025-12-27T01:00:00Z",
    "total_issues": 42
  },
  "issues": [
    {
      "number": 123,
      "title": "Issue title",
      "body": "Full issue body...",
      "body_preview": "First 500 chars...",
      "labels": [{"name": "bug", "color": "d73a4a"}],
      "label_names": ["bug"],
      "age_days": 7,
      "days_since_update": 2,
      "comment_count": 5,
      "is_assigned": false,
      "url": "https://github.com/...",
      "scores": {
        "delegation": null,
        "importance": null,
        "urgency": null,
        "clarity": null,
        "effort": null,
        "priority": null
      },
      "analysis": null
    }
  ]
}
```

## Scoring Criteria

For each issue, evaluate these criteria and assign scores (1-5):

### 🤖 Delegation Potential
*Can this be delegated to an AI coding agent like Copilot?*

| Score | Meaning |
|-------|---------|
| 5 | Perfect for AI: clear scope, well-defined acceptance criteria, isolated change |
| 4 | Good for AI: mostly clear, may need minor clarification |
| 3 | Partial AI assist: AI can help but human judgment needed |
| 2 | Difficult for AI: ambiguous requirements, needs design decisions |
| 1 | Human only: requires context, stakeholder input, or creative direction |

### 🎯 Importance
*How important is this to the project's success?*

| Score | Meaning |
|-------|---------|
| 5 | Critical: security issue, data loss, major feature broken |
| 4 | High: significant user impact, blocking other work |
| 3 | Medium: meaningful improvement, affects subset of users |
| 2 | Low: nice-to-have, minor polish |
| 1 | Minimal: trivial or questionable value |

### ⚡ Urgency  
*How time-sensitive is this?*

| Score | Meaning |
|-------|---------|
| 5 | Immediate: production down, security vulnerability |
| 4 | This week: deadline approaching, blocking release |
| 3 | Soon: should be addressed but not time-critical |
| 2 | Eventually: backlog item, no pressure |
| 1 | Someday/maybe: could be closed or deferred indefinitely |

### 🧹 Clarity
*How well-defined is the issue?*

| Score | Meaning |
|-------|---------|
| 5 | Crystal clear: steps to reproduce, expected vs actual, acceptance criteria |
| 4 | Good: mostly clear, minor questions |
| 3 | Adequate: understandable but needs some investigation |
| 2 | Vague: unclear scope, missing context |
| 1 | Confused: contradictory, rambling, or no actionable request |

### ⏱️ Effort Estimate
*How much work is this likely to be?*

| Score | Meaning |
|-------|---------|
| 5 | Trivial: < 30 minutes, one-line fix |
| 4 | Small: few hours, single file/component |
| 3 | Medium: day or two, multiple files |
| 2 | Large: week+, significant refactoring |
| 1 | Epic: major feature, needs breakdown |

## Priority Formula

```
Priority = (Importance × 3) + (Urgency × 2) + (Clarity × 1.5) + (Delegation × 1) + (Effort × 0.5)
```

Max score: 40 | High priority: ≥30 | Medium: 20-29 | Low: <20

## Workflow

### Standard Workflow

1. Run `./scripts/fetch-issues.sh owner/repo` to fetch issues
2. Analyze the top issues and provide a summary with priorities
3. Run `./scripts/serve.sh` to launch the dashboard in the browser
4. The dashboard auto-loads `triage-data.json` and displays interactive visualizations

### Manual Exploration

1. Run `./scripts/fetch-issues.sh owner/repo`
2. Run `./scripts/serve.sh` (opens http://localhost:8080/dashboard.html)
3. Use the dashboard to explore, filter, and drill into issues
4. Press Ctrl+C in terminal to stop the server when done

## Viewer Features

The `viewer.html` provides:

- **Sort** by priority, age, comments, delegation score
- **Filter** by label, scored/unscored status
- **Search** issues by title or number
- **Score issues** with click-to-edit interface
- **Export** your scored data as JSON
- **Dark mode** GitHub-style interface

## Example LLM Output

When analyzing issues, output updates in this format:

```json
{
  "number": 123,
  "scores": {
    "delegation": 5,
    "importance": 2,
    "urgency": 2,
    "clarity": 5,
    "effort": 5,
    "priority": 24.5
  },
  "analysis": "Trivial docs fix. Perfect for AI delegation - exact change specified."
}
```

Or provide a summary report alongside the JSON updates.

## Files

```
issue-triage/
├── SKILL.md             # This file
├── dashboard.html       # Full analytics dashboard
├── viewer.html          # Simple issue viewer
├── scripts/
│   ├── fetch-issues.sh  # Data fetching script
│   └── serve.sh         # Local server + browser launch
└── triage-data.json     # Generated data (git-ignored)
```

## Notes

- The viewer works offline - all processing is client-side
- Drag and drop JSON files onto the viewer to load them
- Scores persist in the JSON; export to save your work
- For private repos, authenticate `gh` CLI first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patniko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
