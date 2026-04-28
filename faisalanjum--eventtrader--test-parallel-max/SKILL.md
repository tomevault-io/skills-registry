---
name: test-parallel-max
description: Test maximum parallel foreground Task spawns in one response. Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test Parallel Task Spawn (Max)

## Input

`$ARGUMENTS` = `N` (number of tasks to spawn in parallel)

## Steps

1. Record `START_NS` via Bash: `date +%s%N`
2. Spawn **N Task calls in ONE response**:
   - subagent_type: `test-parallel-ping`
   - description: `TEST parallel ping {i}`
   - prompt: `TEST PING {i}`
   - run_in_background: false
3. After all tasks return, record `END_NS` via Bash: `date +%s%N`
4. Write results to:
   `earnings-analysis/test-outputs/parallel-max-$ARGUMENTS.txt`

Format:
```
N: {N}
START_NS: {start}
END_NS: {end}
ELAPSED_NS: {end-start}
NOTE: If elapsed ~2-3s, tasks ran in parallel; if ~2s*N, they ran sequentially.
```

## Rules

- All Task calls must be in the **same response**.
- Do NOT use `run_in_background: true`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
