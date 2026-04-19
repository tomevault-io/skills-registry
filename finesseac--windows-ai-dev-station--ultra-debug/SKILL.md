---
name: ultra-debug
description: Deep systematic debugging with root cause analysis and hypothesis testing Use when this capability is needed.
metadata:
  author: finesseac
---

# Ultra Debug Protocol

A comprehensive debugging methodology that goes beyond surface-level fixes to identify and resolve root causes.

## Step 1: Symptom Analysis

Gather complete information about the failure:

- **What exactly is failing?** (Error message, unexpected behavior, crash)
- **When did it start?** (After a specific change, randomly, under certain conditions)
- **Is it reproducible?** (Always, sometimes, only in specific environments)
- **What changed recently?** Run `git log -10 --oneline` to see recent commits

## Step 2: Context Gathering

Before debugging, understand the system:

- Review relevant code files
- Check recent git changes in affected areas
- Look for similar past issues
- Understand the expected vs actual behavior

## Step 3: Hypothesis Generation

Generate at least 3 hypotheses ranked by likelihood:

1. **[Most Likely]** Description - Reasoning for likelihood
2. **[Possible]** Description - Why this could be the cause
3. **[Less Likely]** Description - Worth checking anyway because...

## Step 4: Systematic Elimination

For each hypothesis (starting with most likely):

1. Design a minimal test that would confirm/refute the hypothesis
2. Execute the test
3. Record the result
4. Update probability rankings based on evidence
5. Move to next hypothesis if not confirmed

## Step 5: Root Cause Identification

Once you find the issue:

- Trace the COMPLETE causal chain (not just the immediate trigger)
- Ask "why" at least 3 times to get to the root
- Document the root cause clearly

## Step 6: Fix Implementation

Apply a proper fix:

- Make the MINIMAL change needed to fix the root cause
- Avoid band-aid fixes that only address symptoms
- Ensure the fix doesn't introduce new issues
- Add a regression test if possible

## Step 7: Verification

Confirm the fix:

- Verify the original issue is resolved
- Test edge cases related to the fix
- Run existing tests to check for regressions
- Document what was fixed and why

## Step 8: Prevention

Learn from the bug:

- Why wasn't this caught earlier?
- What process improvement could prevent similar issues?
- Should tests be added/improved?
- Should documentation be updated?

## Debugging Commands Reference

```powershell
# Recent git changes
git log -10 --oneline
git diff HEAD~5

# Find where something was introduced
git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>

# Search for patterns in code
grep -r "pattern" --include="*.ts"

# Check running processes
Get-Process | Where-Object { $_.ProcessName -like "*node*" }

# Check port usage
netstat -ano | findstr :3000
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finesseac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
