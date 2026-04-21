---
name: systematic-debugging
description: Use when encountering any bug, test failure, or unexpected behavior, before proposing fixes
metadata:
  author: jbro
---

# Systematic Debugging

## Overview

Random fixes waste time and create new bugs. Quick patches mask underlying issues.

**Core principle:** ALWAYS find root cause before attempting fixes. Symptom fixes are failure.

**Violating the letter of this process is violating the spirit of debugging.**

## The Iron Law

```
NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST
```

If you haven't completed Phase 1, you cannot propose fixes.

## When to Use

Use for **any** technical issue: test failures, bugs, unexpected behavior, performance problems,
build failures, integration issues.

**Never skip when:** issue seems simple, you're under time pressure, or you've already tried multiple
fixes. Systematic debugging is FASTER than thrashing. Emergencies make guessing tempting — resist.

## The Four Phases

You MUST complete each phase before proceeding to the next.

### Phase 1: Root Cause Investigation

**BEFORE attempting ANY fix:**

1. **Read Error Messages Carefully**
   - Don't skip past errors or warnings
   - Read stack traces completely; note line numbers, file paths, error codes

2. **Reproduce Consistently**
   - Can you trigger it reliably? If not → gather more data, don't guess

3. **Check Recent Changes**
   - What changed that could cause this? Git diff, recent commits, config changes

4. **Gather Evidence in Multi-Component Systems**

   **WHEN system has multiple components:**

   Add diagnostic instrumentation at each component boundary before proposing fixes:
   ```
   For EACH component boundary:
     - Log what data enters component
     - Log what data exits component
     - Verify environment/config propagation

   Run once to gather evidence → identify failing component → investigate that component
   ```

   **Example (CI → build → signing pipeline):**
   ```bash
   echo "=== Secrets in workflow: ===" && echo "IDENTITY: ${IDENTITY:+SET}${IDENTITY:-UNSET}"
   echo "=== Env vars in build: ===" && env | grep IDENTITY || echo "IDENTITY not in environment"
   echo "=== Keychain state: ===" && security find-identity -v
   ```
   This reveals which layer fails (workflow ✓, workflow → build ✗).

5. **Trace Data Flow**

   **WHEN error is deep in call stack:**

   Read `root-cause-tracing.md` in this skill's directory for the complete backward tracing technique.

   Quick version: where does bad value originate? What called this with bad value? Keep tracing
   up until you find the source. Fix at source, not at symptom.

### Phase 2: Pattern Analysis

1. **Find Working Examples** — locate similar working code in the same codebase
2. **Compare Against References** — if implementing a pattern, read the reference COMPLETELY; don't skim
3. **Identify Differences** — list every difference between working and broken, however small
4. **Understand Dependencies** — settings, config, environment assumptions

### Phase 3: Hypothesis and Testing

1. **Form Single Hypothesis** — "I think X is the root cause because Y"; write it down
2. **Test Minimally** — smallest possible change to test the hypothesis; one variable at a time
3. **Verify Before Continuing** — worked? → Phase 4. Didn't work? → new hypothesis
4. **When You Don't Know** — say so; don't pretend; ask for help or research more

### Phase 4: Implementation

**Fix the root cause, not the symptom:**

1. **Create Failing Test Case** — simplest possible reproduction; use the `test-driven-development` skill
2. **Implement Single Fix** — one change; no "while I'm here" improvements
3. **Verify Fix** — test passes; no other tests broken
4. **If Fix Doesn't Work** — STOP; count fixes tried:
   - < 3: return to Phase 1 with new information
   - **≥ 3: STOP — question the architecture (step 5)**

5. **If 3+ Fixes Failed: Question Architecture**

   Pattern indicating architectural problem:
   - Each fix reveals new shared state/coupling in a different place
   - Fixes require massive refactoring to implement
   - Each fix creates new symptoms elsewhere

   **STOP: discuss with your human partner before attempting more fixes.**
   This is a wrong architecture, not a failed hypothesis.

## Red Flags — STOP and Follow Process

If you catch yourself thinking any of these:
- "Quick fix for now, investigate later"
- "Just try changing X and see if it works"
- "Add multiple changes, run tests"
- "Skip the test, I'll manually verify"
- "It's probably X, let me fix that"
- "I don't fully understand but this might work"
- "Pattern says X but I'll adapt it differently"
- "Here are the main problems: [lists fixes without investigation]"
- Proposing solutions before tracing data flow
- **"One more fix attempt"** (when already tried 2+)
- **Each fix reveals new problem in a different place**
- "Emergency, no time for process" (systematic IS faster)
- "I see the problem" (symptoms ≠ root cause)

**ALL of these mean: STOP. Return to Phase 1.**

**If 3+ fixes failed:** Question the architecture (Phase 4.5).

## your human partner's Signals You're Doing It Wrong

Watch for these redirections:
- "Is that not happening?" — you assumed without verifying
- "Will it show us...?" — you should have added evidence gathering
- "Stop guessing" — you're proposing fixes without understanding
- "Ultrathink this" — question fundamentals, not just symptoms
- "We're stuck?" (frustrated) — your approach isn't working

When you see these: STOP. Return to Phase 1.

## Quick Reference

| Phase | Key Activities | Success Criteria |
|-------|---------------|------------------|
| **1. Root Cause** | Read errors, reproduce, check changes, gather evidence | Understand WHAT and WHY |
| **2. Pattern** | Find working examples, compare | Identify differences |
| **3. Hypothesis** | Form theory, test minimally | Confirmed or new hypothesis |
| **4. Implementation** | Create test, fix, verify | Bug resolved, tests pass |

## When Process Reveals "No Root Cause"

If investigation reveals the issue is truly environmental, timing-dependent, or external:
1. Document what you investigated
2. Implement appropriate handling (retry, timeout, error message)
3. Add monitoring/logging for future investigation

Note: 95% of "no root cause" cases are incomplete investigation.

## Supporting Techniques

Available in this directory:

- **`root-cause-tracing.md`** — trace bugs backward through call stack to find original trigger
- **`defense-in-depth.md`** — add validation at multiple layers after finding root cause
- **`condition-based-waiting.md`** — replace arbitrary timeouts with condition polling

**Related skills:**
- **test-driven-development** — for creating the failing test case (Phase 4, Step 1)
- **verification-before-completion** — verify fix worked before claiming success

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
