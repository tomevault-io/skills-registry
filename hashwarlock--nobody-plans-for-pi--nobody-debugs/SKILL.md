---
name: nobody-debugs
description: Use when encountering any bug, test failure, or unexpected behavior, before proposing fixes
metadata:
  author: hashwarlock
---

# Systematic Debugging

## Overview

4-phase process: reproduce, isolate, identify root cause, fix with test.

**Core principle:** Random fixes waste hours. Systematic tracing takes minutes.

**Announce at start:** "I'm using the nobody-debugs skill to trace this."

## Phase 1: Reproduce

Before anything else, get a reliable reproduction:

1. Find the exact command/action that triggers the bug
2. Note the exact error message and behavior
3. Confirm it reproduces consistently
4. If intermittent, note frequency and conditions

**Cannot reproduce?** Gather more data before proceeding. Don't guess.

## Phase 2: Isolate

Narrow down where the problem lives:

1. **Binary search** — Comment out half the code, does it still fail?
2. **Simplify inputs** — Find the minimal input that triggers the bug
3. **Check boundaries** — Which module/function/line is the boundary between working and broken?
4. **Read error traces** — Follow the stack trace to the actual failure point

**Goal:** "The bug is in function X, triggered when Y happens."

## Phase 3: Root Cause

Trace backward from the symptom to the origin:

1. Start at the crash/error point
2. Ask: "What state caused this to fail?"
3. Trace that state backward: "Where was this value set?"
4. Repeat until you find the original cause

**Root cause checklist:**
- Can you explain WHY the bug happens, not just WHERE?
- If you fix this, will the symptom definitely disappear?
- Is this the deepest cause, or just another symptom?

**Common traps:**
- Fixing the symptom, not the cause
- Stopping at the first suspicious thing
- Assuming without evidence

## Phase 4: Fix with Test

1. **Write failing test** reproducing the bug (TDD RED)
2. **Watch it fail** with the exact symptom
3. **Apply minimal fix** addressing root cause
4. **Watch it pass** (TDD GREEN)
5. **Check for regressions** — run full test suite
6. **Add defense** — validation, assertions, logging at the failure point

## When Stuck

| Problem | Action |
|---------|--------|
| Can't reproduce | Add logging, try different environments |
| Too many variables | Isolate one variable at a time |
| Fix broke something else | Revert, understand dependencies first |
| "No root cause found" | 95% of the time = incomplete investigation |

## Red Flags

- Proposing fixes before understanding the root cause
- "Let me try this and see if it works"
- Changing multiple things at once
- Not writing a regression test
- Fixing symptoms instead of causes

## Real-World Impact

- Systematic approach: 15–30 minutes to fix
- Random fixes approach: 2–3 hours of thrashing
- First-time fix rate: ~95% vs ~40%
- New bugs introduced: Near zero vs common

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashwarlock) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
