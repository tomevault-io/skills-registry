---
name: test-re-tasks-in-fork
description: Retest 2026-02-05: Can forked skill use TaskCreate/List/Get/Update? Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: Task management tools in fork

1. Call TaskList to see existing tasks
2. Call TaskCreate with subject "retest-from-fork" and description "created by forked skill"
3. Call TaskList again to confirm it was created
4. Write to earnings-analysis/test-outputs/test-re-tasks-in-fork.txt:
   - "TASKLIST: WORKS" or "TASKLIST: BLOCKED"
   - "TASKCREATE: WORKS" or "TASKCREATE: BLOCKED"
   - Number of tasks visible
   - The task ID of the one you created

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
