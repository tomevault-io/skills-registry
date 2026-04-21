---
name: workflow-review
description: Run comprehensive multi-dimensional code reviews and synthesize findings by severity. Use when this capability is needed.
metadata:
  author: abpaul
---

# Workflow Review

## Review Dimensions

- Architecture and design integrity
- Simplicity and maintainability
- Security and data integrity
- Performance and scalability
- Frontend race/timing risks when applicable
- Rails-specific correctness (controllers/models/jobs, params/authz, Hotwire behavior)

## Workflow

1. Determine review target (branch/PR/files).
2. Analyze changed code across all applicable dimensions.
3. Prioritize findings by severity with file references.
4. Capture unresolved items in the active sprint doc (`docs/sprints/*.md`) under a findings/follow-up section.
5. If scope exceeds current sprint, create a new sprint doc instead of scattered todo files.

## Rails Review Checklist

- Controller boundaries remain thin; domain logic stays in model/service layers.
- Strong params and authorization checks cover all mutable endpoints.
- ActiveRecord hot paths avoid N+1 and include index-aware query patterns.
- Jobs are idempotent and retry-safe.
- Turbo Frames/Streams and Stimulus lifecycle behavior remain correct under partial updates.
- Tailwind + daisyUI usage stays token-driven; no default daisyUI theme ships unchanged.
- Phlex component changes preserve primitive composability and controlled variant APIs.
- MD3 usage remains structural (anatomy/state/accessibility/interaction), not visual identity copying.
- UI deltas include screenshot-polish evidence for affected web/native shells.

## Context Discipline

- Start from changed files and hot paths; expand scope only when findings indicate systemic issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
