---
name: ralph-graceful-exit
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Ralph Graceful Exit

## Purpose

Detect when a Ralph Loop iteration has truly completed its task, beyond just the `<promise>DONE</promise>` tag. This skill implements 4-signal detection to verify genuine completion.

## Integrations

| Type | References |
|------|------------|
| hooks | ralph-circuit-breaker, ralph-rate-limiter, ralph-activity-log |
| plugins | ralph-loop@claude-plugins-official |

## 4-Signal Detection

| Signal | Threshold | Detection Method |
|--------|-----------|------------------|
| test_loops | ≥3 consecutive | Only running tests, no code changes |
| done_signals | ≥2 occurrences | "done", "complete", "finished" in output |
| completion_indicators | ≥2 patterns | Strong completion language patterns |
| fix_plan_check | all items ✓ | All @fix_plan.md items checked |

## Detection Logic

When evaluating completion:

1. **Check activity log** for test-only iterations
   ```bash
   grep -c "test\|pytest\|jest\|npm test" ~/.ralph-state/activity.log
   ```

2. **Scan recent output** for done_signals patterns
   - "done", "complete", "finished", "all tests pass"
   - "implementation complete", "task finished"

3. **Look for completion_indicators** in conversation
   - Strong completion language
   - Summary of what was accomplished
   - No pending TODO items mentioned

4. **Verify fix_plan** if exists
   - Check if @fix_plan.md exists
   - Verify all checkbox items are checked `[x]`

## Exit Recommendation

If ≥2 signals detected, recommend outputting:

```
<promise>DONE</promise>
```

## Signal Weights

| Signal | Weight | Confidence |
|--------|--------|------------|
| fix_plan_check (all ✓) | 1.0 | High |
| test_loops ≥3 | 0.8 | High |
| done_signals ≥2 | 0.6 | Medium |
| completion_indicators ≥2 | 0.5 | Medium |

Total weight ≥1.5 → Strong recommendation to exit
Total weight ≥1.0 → Suggest checking completion status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
