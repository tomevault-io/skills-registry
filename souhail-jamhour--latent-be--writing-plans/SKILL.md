---
name: writing-plans
description: Use when the design phase is complete. Generates ultra-specific implementation plans for Go/Python backend services. Assumes the executor knows the stack but lacks project context. Enforces "Atomic Commits" and strict file paths.
metadata:
  author: souhail-jamhour
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our specific codebase but is proficient in Go and Python. Document everything they need to know: exact file paths, complete code snippets (idiomatic Go/Python), and specific Docker commands.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Save plans to:** `docs/plans/Sprint-N/YYYY-MM-DD-<feature-name>/plan.md`

## Bite-Sized Task Granularity

**Each step is one atomic action (5-15 minutes):**

- "Define Struct/Schema" - step
- "Implement Interface Method" - step
- "Verify (Go Test / Docker Log Check)" - step

## Plan Document Header

**Every plan MUST start with this header:**

# [Feature Name] Implementation Plan

> **Directive:** Follow this plan strictly. Do not deviate from the architecture without asking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach, referencing the Design Doc]

**Tech Stack:** Go 1.22 (Gin, pgx), Python 3.11, PostgreSQL 16, Docker Compose

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/souhail-jamhour) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
