---
name: document-codeflow-update
description: Detect git diffs and identify/update impacted code flow documentation. Analyze staged/unstaged changes, investigate impact, and sync existing sequence diagrams and explanations. Use when asked to "update docs", "sync with diffs", "refresh code flow", or update docs after code changes. Use when this capability is needed.
metadata:
  author: luckgakidz
---

# Documentation Update Skill

This skill updates existing code flow documents in `docs/codeflow/` to match code changes.

## When to Use This Skill

- Syncing docs after code changes
- Checking docs are current before PR
- Identifying and updating docs impacted by refactoring

## Prerequisites

- Existing code flow docs are present in `docs/codeflow/`
- Changes are tracked in a Git repository

## Step-by-Step Workflows

### Workflow: Diff-Based Documentation Update

1. **Check diffs**
   - Review Git diffs (staged/unstaged) with `#changes`
   - Get changed file list

2. **Classify changes**
   - Minor only (comments, formatting, type annotations) -> **No update needed**
   - Logic changes (function add/remove, branch changes, call target changes) -> **Update needed**

3. **Identify impact scope**
   - Use `#search` to find related docs in `docs/codeflow/`
   - Use `#usages` to find callers of changed functions/classes
   - List impacted document files

4. **Review existing docs**
   - Read impacted docs with `#readFile`
   - Identify gaps between current docs and actual code

5. **Update docs**
   - Update sequence diagrams with `#editFiles` to match latest code
   - Update explanations (step descriptions, file names, function names)
   - Update **Markdown links** for file paths/functions (rename/move/line changes)
   - Record update date in docs

6. **Report results**
   - Output summary of updated docs
   - Use `#problems` to check for code issues

## Criteria: Cases Requiring Updates

| Change Type | Update Required |
|-------------|:---------------:|
| Comment/JSDoc changes | ❌ |
| Formatting/indentation | ❌ |
| Added type annotations | ❌ |
| Variable rename | ⚠️ Only if function signature is affected |
| File name/path change | ✅ Link updates required |
| Function/method line number changes | ✅ Update link `#Lline` |
| Function add/remove | ✅ |
| Branching changes | ✅ |
| Call target changes | ✅ |
| Event/command additions | ✅ |
| Error handling changes | ✅ |

## Update Timestamp Format

Add/update this line in the header area (before overview):

```markdown
> Last Updated: YYYY-MM-DD - [summary of changes]
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Related docs not found | Full-text search under `docs/codeflow/` by changed file names |
| Changes are too broad | Prioritize high-impact changes first, defer minor ones |
| Feature has no docs yet | Recommend creating new docs with `document.codeflow.analyze` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luckgakidz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
