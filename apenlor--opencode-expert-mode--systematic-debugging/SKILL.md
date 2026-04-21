---
name: systematic-debugging
description: Use when encountering any bug, test failure, or unexpected behavior — find root cause before proposing any fix
metadata:
  author: apenlor
---

# Systematic Debugging

## Overview
Random fixes waste time and mask underlying issues. **Core principle:** Find root cause before attempting any fix. Symptom fixes are failure.

## The Iron Law
```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

## When to Use
Use for ANY technical issue. **Especially** when under time pressure, when you've already tried multiple fixes, or when you don't fully understand the issue.

## The Four Phases

Complete each phase before proceeding.

### Phase 1: Root Cause Investigation
1. **Read Error Messages** — note line numbers, file paths, stack traces
2. **Reproduce Consistently** — identify exact steps to trigger the bug reliably
3. **Check Recent Changes** — review git diffs, new dependencies, config changes
4. **Gather Evidence** — in multi-component systems, log at boundaries to isolate the failing layer
5. **Trace Data Flow** — for deep errors, trace backward through the call chain to the original trigger

### Phase 2: Pattern Analysis
1. **Find Working Examples** — locate similar working code in the codebase
2. **Compare Against References** — read reference implementations completely
3. **Identify Differences** — list every difference between working and broken states

### Phase 3: Hypothesis and Testing
1. **Form Single Hypothesis** — "I think X is the root cause because Y"
2. **Test Minimally** — make the SMALLEST change to test the hypothesis
3. **Verify** — if it fails, form a NEW hypothesis; never stack fixes

### Phase 4: Implementation
1. **Create Failing Test** — simplest reproduction (use `test-driven-development`)
2. **Implement Single Fix** — address the identified root cause only
3. **Verify Fix** — test passes and no regressions
4. **If 3+ Fixes Fail** — STOP; question the architecture; discuss with your human partner

## Red Flags — Return to Phase 1
- "Quick fix for now"
- Adding multiple changes at once
- Proposing solutions before tracing data flow
- Each fix reveals a new problem elsewhere
- Skipping test creation

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "Issue is simple" | Simple bugs have root causes too |
| "Emergency" | Systematic is FASTER than thrashing |
| "Test after fix" | Untested fixes don't stick |
| "3+ failures" | Architectural problem — question the pattern |

## Quick Reference

| Phase | Key Activity | Success Criteria |
|-------|-------------|------------------|
| **1. Root Cause** | Read errors, reproduce, trace data flow | Understand WHAT and WHY |
| **2. Pattern** | Find working examples, compare | Identified differences |
| **3. Hypothesis** | Form theory, test minimally | Confirmed hypothesis |
| **4. Implementation** | Create test, fix, verify | Bug resolved, tests pass |

## Supporting Techniques

### Root Cause Tracing
Trace backward through the call chain until you find the original trigger, then fix at the source — not where the symptom appears.

1. Observe the symptom (error message, wrong output)
2. Find the immediate cause (what code directly produces this?)
3. Ask: what called this, and with what values?
4. Keep tracing up the call stack
5. Find where the bad value originates — fix there

When you can't trace manually, add instrumentation at the suspect boundary:
```typescript
console.error('DEBUG:', { inputValue, cwd: process.cwd(), stack: new Error().stack });
```
Use `console.error` in tests (not logger — may not show).

### Defense in Depth
Validate at every layer data passes through. Make the bug structurally impossible.

| Layer | Purpose |
|-------|---------|
| Entry point | Reject invalid input at API boundary |
| Business logic | Ensure data makes sense for this operation |
| Environment guards | Prevent dangerous operations in specific contexts (e.g., refuse destructive ops outside temp dir during tests) |
| Debug instrumentation | Capture context for forensics |

### Condition-Based Waiting
Wait for the actual condition, not a guess about timing.

| Scenario | Pattern |
|----------|---------|
| Wait for event | `waitFor(() => events.find(e => e.type === 'DONE'))` |
| Wait for state | `waitFor(() => machine.state === 'ready')` |
| Wait for file | `waitFor(() => fs.existsSync(path))` |

Generic polling function: poll every 10ms, timeout at 5000ms, throw with descriptive message on timeout.

## Related Skills
- **test-driven-development** — for creating the failing test in Phase 4
- **completing-work** — verify fix worked before claiming success

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/apenlor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
