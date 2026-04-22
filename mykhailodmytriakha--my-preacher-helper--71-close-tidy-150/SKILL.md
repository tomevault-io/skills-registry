---
name: 71-close-tidy-150
description: [71] CLOSE. Quick, safe cleanup after completing a milestone. Fix objective issues only (syntax errors, dead code, poor naming). Must be <5% of main task time, <30 seconds per fix, and reversible. Use after key points, not after every small change. Use when this capability is needed.
metadata:
  author: mykhailodmytriakha
---

# Close-Tidy 150 Protocol

**Core Principle:** Clean as you go — but safely. After milestones, fix obvious issues quickly. Objective problems only. Time-boxed. Reversible. User-approved.

## What This Skill Does

When you invoke this skill, you're asking AI to:
- **Scan for obvious issues** — Syntax, dead code, naming
- **Time-box strictly** — ≤5% of main task time
- **Stay safe** — Only reversible, objective fixes
- **Get approval** — User confirms before execution
- **Document impact** — Report what was improved

## The 150% Tidy Rule

| Dimension | 100% Core | +50% Enhancement |
|-----------|-----------|------------------|
| **Issues** | Objective defects only | + Prove each is defect |
| **Time** | ≤5% of main task | + <30 sec per fix |
| **Safety** | All fixes reversible | + Tests still pass |
| **Scope** | No expansion | + User approves list |

## What Qualifies as Tidy-Up

```
✅ ALLOWED (Objective Defects)
├── Syntax Errors: Clear compilation issues
├── Dead Code: Unused, unreachable code
├── Poor Naming: Confusing variable/function names
├── Unused Imports: Import statements not used
├── Obvious Typos: Clear spelling mistakes
└── Simple Formatting: Obvious style violations

❌ NOT ALLOWED (Scope Creep)
├── Refactoring: Changing code structure
├── New Features: Adding functionality
├── Optimization: Performance improvements
├── Architecture: Changing design patterns
├── Complex Changes: Anything needing analysis
└── Debatable Issues: Subjective improvements
```

## Time Limits

| Fix Type | Time Limit | Safety Check | Revert Ease |
|----------|------------|--------------|-------------|
| **Syntax Fix** | <10 sec | Auto-check | Instant |
| **Naming Fix** | <15 sec | Code review | Instant |
| **Dead Code** | <20 sec | Reference check | Instant |
| **Unused Import** | <10 sec | Compile check | Instant |
| **Simple Format** | <15 sec | Visual check | Instant |
| **Complex Change** | ❌ Forbidden | N/A | N/A |

**Rule:** If it takes >30 seconds to verify safety → NOT a tidy-up item.

## When to Use This Skill

**TRIGGER:** Only after **Key Point Milestones**:
- ✅ Feature implementation complete
- ✅ Major refactoring done
- ✅ Bug fix verified
- ✅ Phase of plan completed

**NOT TRIGGER:**
- ❌ After every file edit
- ❌ During active development
- ❌ Before understanding the code
- ❌ When unsure about impact

## Execution Protocol

### Step 1: MILESTONE CHECK
```
🏁 **MILESTONE VERIFICATION**

**Completed:** [What milestone was reached]
**Main Task Time:** [How long the main work took]
**Tidy Budget:** [5% of main task = X minutes]
```

### Step 2: OBSERVATION SCAN
Review for obvious issues:
```
🔍 **SCAN RESULTS**

**Issues Found:**
1. [Issue]: [Location] - [Fix time estimate]
2. [Issue]: [Location] - [Fix time estimate]
3. [Issue]: [Location] - [Fix time estimate]

**Total Fixes:** [N]
**Total Time Estimate:** [X minutes]
**Within Budget:** ✅ Yes | ❌ No (reduce scope)
```

### Step 3: SAFETY VERIFICATION
For each issue:
```
🛡️ **SAFETY CHECK**

Issue: [Description]
├── Objective Defect: ✅ Provable | ❌ Subjective
├── Reversible: ✅ Easy revert | ❌ Complex
├── Tests Pass: ✅ Verified | ⚠️ Need to check
└── No Side Effects: ✅ Contained | ❌ Cascading

Safe to Fix: ✅ Yes | ❌ No
```

### Step 4: CLEANUP PACKAGE
Present for approval:
```
🧹 **TIDY-UP PROPOSAL**

**Milestone:** [What was completed]
**Time Budget:** [X minutes] (5% of main task)

**Proposed Fixes:**
1. ✅ [Fix 1]: [Description] - [X sec]
2. ✅ [Fix 2]: [Description] - [X sec]
3. ✅ [Fix 3]: [Description] - [X sec]

**Total Time:** [Y minutes]
**All Reversible:** ✅ Yes
**All Objective:** ✅ Yes

**Approve cleanup?** (Yes / No / Modify list)
```

### Step 5: CONTROLLED EXECUTION
Apply fixes one by one:
- Execute single fix
- Verify immediately
- Document change
- Stop if issues arise

### Step 6: REPORT
```
🧹 **TIDY-UP 150 COMPLETE**

**Fixes Applied:**
✅ [Fix 1]: [What was done]
✅ [Fix 2]: [What was done]
✅ [Fix 3]: [What was done]

**Time Spent:** [X minutes] ([Y% of budget])

**Verification:**
├── Tests: ✅ Passing
├── Functionality: ✅ Preserved
├── Revert Ready: ✅ Yes
└── No Side Effects: ✅ Confirmed

**Impact:**
├── Code Quality: Improved
├── Technical Debt: Reduced
└── Future Benefit: [Description]
```

## Output Format

Proposal:
```
🧹 **TIDY-UP 150 PROPOSAL**

**After Milestone:** [What was completed]
**Budget:** [X min] (5% of [Y min] main task)

**Fixes:**
| # | Issue | Location | Time | Safe |
|---|-------|----------|------|------|
| 1 | [Issue] | [File:Line] | Xs | ✅ |
| 2 | [Issue] | [File:Line] | Xs | ✅ |

**Total:** [X sec] | **All Safe:** ✅

**Approve?** (Yes / No / Modify)
```

Report:
```
🧹 **TIDY-UP 150 DONE**

**Applied:** [N] fixes in [X] minutes
**Tests:** ✅ Passing
**Quality:** Improved

**Changes:**
- [File]: [What changed]
- [File]: [What changed]
```

## Operational Rules

1. **KEY POINT ONLY:** Trigger only after major milestones
2. **TIME BOUND:** Never exceed 5% of main task time
3. **OBJECTIVE ONLY:** Provable defects, not opinions
4. **SAFETY FIRST:** Every fix must be verifiable safe
5. **USER APPROVAL:** Get permission before executing
6. **SCOPE CONTROL:** No expansion beyond identified issues

## Failure Modes & Recovery

| Failure | Detection | Recovery |
|---------|-----------|----------|
| **Scope Creep** | Fixing more than listed | Stop, create separate task |
| **Time Overrun** | Exceeding 5% budget | Pause, reschedule remaining |
| **Safety Breach** | Fix introduces issues | Immediate revert |
| **Unapproved** | Fixing without consent | Revert, get approval |

## Examples

### ❌ Bad Tidy-Up
```
Milestone: Small bug fix (10 minutes)

"Tidy-up":
- Refactored entire module architecture
- Added new helper functions
- Changed error handling approach

Time: 3 hours (1800% of main task!)
Result: Introduced new bugs, delayed delivery
```

### ✅ Good Tidy-Up
```
🧹 TIDY-UP 150 PROPOSAL

After Milestone: Feature implementation (2 hours)
Budget: 6 min (5% of 120 min)

Fixes:
| # | Issue | Location | Time | Safe |
|---|-------|----------|------|------|
| 1 | Unused import | auth.ts:3 | 5s | ✅ |
| 2 | Typo in var name | user.ts:45 | 10s | ✅ |
| 3 | Dead function | utils.ts:89 | 15s | ✅ |

Total: 30 sec | All Safe: ✅

User: "Yes"

🧹 TIDY-UP 150 DONE

Applied: 3 fixes in 30 seconds
Tests: ✅ Passing
Quality: Improved

Changes:
- auth.ts: Removed unused 'lodash' import
- user.ts: Renamed 'usrData' → 'userData'
- utils.ts: Removed unused 'legacyFormat()' function
```

## Relationship to Other Skills

- **gated-exec-150** → Completes main work
- **tidy-up-150** → Quick cleanup after milestone
- **integrity-check-150** → Full quality check

---

**Remember:** Tidy-up is housekeeping, not renovation. Quick fixes for obvious issues. If you're thinking about it for more than 30 seconds, it's not a tidy-up item — it's a separate task.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mykhailodmytriakha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
