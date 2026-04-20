---
name: review-patterns
description: > Use when this capability is needed.
metadata:
  author: mrge-io
---

# Team Review Patterns

Surface the team's coding conventions and review preferences from cubic's learned patterns.

## When To Activate

- User is writing new code and wants it to match team standards
- User asks about team coding conventions or review preferences
- User is preparing code for review and wants to preemptively fix issues
- User wonders why cubic flags certain patterns

## Inputs

- **Context** (optional): What the user is currently working on, to filter relevant learnings.

## Instructions

### 1. Detect the repository

```bash
git remote get-url origin
```

Extract the owner and repo name.

### 2. Fetch learnings

Call `list_learnings` with the owner and repo.

### 3. Filter by relevance

Match learning categories to the user's current task:

- Prioritize high-confidence learnings
- Focus on learnings from senior reviewer analysis (highest signal)
- If the user is working on a specific file or module, prioritize related learnings

### 4. Enrich if needed

If a specific learning is highly relevant, call `get_learning` for full details including the original review feedback that generated it.

## Output Format

Present learnings as team preferences, not rigid rules. Group related learnings and include confidence levels.

```
## Team Patterns for this repo

### Error Handling (High Confidence)
- **Always wrap external API calls in try/catch** — The team prefers explicit error
  handling over global middleware. Learned from senior reviewer feedback.
- **Use custom error classes** — Throw `AppError` subclasses, not raw `Error`.

### Naming (Medium Confidence)
- **Boolean variables use `is`/`has` prefix** — e.g., `isActive`, `hasPermission`.

### Testing (High Confidence)
- **Integration tests for all API endpoints** — Unit tests alone are not sufficient
  for route handlers.

_3 learnings from senior reviewer analysis · 1 from user feedback_
```

If no learnings exist, explain that cubic learns patterns over time from senior reviewer feedback.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrge-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
