---
name: sync-knowledge
description: Synchronizes knowledge with source code changes Use when this capability is needed.
metadata:
  author: sato-dev1234
---

# /sync-knowledge

Orchestrator for knowledge synchronization with source code changes.

## Progress Checklist

```
- [ ] Step 1: Validate knowledge state
- [ ] Step 2: Detect source changes
- [ ] Step 3: Report changes
- [ ] Step 4: Update knowledge (interactive)
- [ ] Step 5: Rebuild index
- [ ] Step 6: Report
```

## Steps

1. Validate knowledge state:
   - Check $STATE_DIR exists
   - If not exists → Error: ".knowledge-state not found. Run /collect-knowledge first.", END

2. Detect source changes:
   - For each state file in $STATE_DIR:
     - Read .knowledge-state/{category}/{id}.json
     - Execute: `git diff --name-status {commit}..HEAD`
     - Compare refs with changed files
     - Classify: high (D:deleted) / medium (M,R:modified/renamed) / low (unchanged)
   - Build CHANGES list

3. Report changes:
   - Display summary:
     - High impact: files requiring immediate knowledge update
     - Medium impact: files potentially needing review
     - Low impact: no action needed

4. Update knowledge (interactive):
   - For each high/medium impact item:
     - AskUserQuestion: "Update this knowledge? [Y/n]"
     - If Y:
       - Read source changes
       - Generate updated content
       - Get current commit: `git rev-parse HEAD`
       - Invoke knowledge-writer:
         ```
         OPERATION=update
         CATEGORY=$CATEGORY
         ID=$ID
         CONTENT=$UPDATED_CONTENT
         COMMIT_HASH=$COMMIT_HASH
         ```

5. Rebuild index:
   - Invoke knowledge-writer: `OPERATION=rebuild-index`

6. Report:
   - Display summary: knowledge files updated, new commit hash recorded
   - END

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
