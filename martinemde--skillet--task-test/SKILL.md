---
name: task-test
description: Test skill that exercises the Task tool system to create and manage tasks Use when this capability is needed.
metadata:
  author: martinemde
---

# Task System Test Skill

This skill exercises the Task tool system. You _MUST_ use TaskCreate, TaskUpdate, and TaskList to manage tasks. Set awn owner for the tasks to "haiku".

## Instructions

**Step 1: Create tasks IN PARALLEL** (all three TaskCreate calls in one response):

- TaskCreate: subject="List Go files", description="Count the number of \*.go files", activeForm="Listing Go files"
- TaskCreate: subject="Check formatting", description="Run go fmt on all the files", activeForm="Checking formatting"
- TaskCreate: subject="Count lines", description="Count # of lines in \*.go files", activeForm="Counting lines"

**Step 2: List tasks** to see what was created.

**Step 3: Complete each task**

Summarize results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/martinemde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
