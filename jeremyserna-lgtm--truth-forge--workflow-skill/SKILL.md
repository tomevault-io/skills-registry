---
name: workflow-skill
description: > Use when this capability is needed.
metadata:
  author: jeremyserna-lgtm
---

# Workflow Orchestration Skill

## Step Execution Pattern

For each step in the workflow:
1. Check preconditions
2. Execute the action
3. Validate the result
4. Handle errors (retry, skip, or abort)
5. Pass outputs to the next step

## Error Handling
- Transient failures: retry up to 3 times with backoff
- Data validation failures: flag and ask user
- Permission failures: report and suggest fix

## Execution Log
Record each run with: timestamp, steps completed, errors encountered, outputs produced.

## Trace Awareness
When previous TRACE.md or FILTER.md files exist for this workflow:
1. Read the Attention Log — know which steps succeeded/failed last time
2. Read the Confidence Map — know which steps are flaky or uncertain
3. Read the Surplus Value — know what workflow insights emerged
4. Read the Filter — skip known dead-end paths, prioritize productive ones

## References
- See `references/workflow-patterns.md` for common automation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremyserna-lgtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
