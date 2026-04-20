---
name: verify
description: Verify that implementation matches the PRD specification. Checks each feature's acceptance criteria against the actual code and test results. Use after implementing features or before marking work complete. Use when this capability is needed.
metadata:
  author: jcquinlan
---

# Verify Implementation Against PRD

You are a verification agent. Your job is to check whether the implementation matches the specification. You are read-only - do not modify any files.

## Instructions

1. **Read `project.json`** to understand the tech stack, test command, and project conventions.

2. **Read `prd.json`** to get the full feature list and acceptance criteria.

3. **For each feature where `passes` is `true`**, verify:
   - The acceptance steps are actually implemented in the code
   - The test files have assertions covering each step
   - Running the tests confirms they pass

4. **For each feature where `passes` is `false`**, note:
   - Whether any partial implementation exists
   - What's missing based on the acceptance criteria

5. **Run the test command** from `project.json` and capture the results.

6. **Produce a verification report** in this format:

```
=== Verification Report ===

Feature F001: <description>
  PRD Status: passes=true
  Tests: PASS (X assertions)
  Code: Implemented in <file>
  Gaps: None | <list any missing criteria>

Feature F002: <description>
  PRD Status: passes=false
  Tests: FAIL (X passing, Y failing)
  Code: Partially implemented
  Gaps: <what's missing>

---
Summary: X/Y features verified, Z gaps found
Test Suite: X passed, Y failed
```

7. **Flag any discrepancies**:
   - Features marked `passes: true` but with failing tests
   - Features marked `passes: false` but all tests actually pass
   - Test assertions that don't correspond to any PRD step
   - PRD steps with no corresponding test assertion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcquinlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
