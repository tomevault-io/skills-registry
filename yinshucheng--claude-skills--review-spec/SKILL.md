---
name: review-spec
description: Multi-model cross-review for spec documents. Uses fresh Claude context + Gemini to independently review requirements.md, design.md, and tasks.md, then merges findings into a unified report with consensus markers. Use when this capability is needed.
metadata:
  author: yinshucheng
---

# Review Spec — Multi-Model Cross-Review

Review a spec directory (requirements.md + design.md + tasks.md) using multiple AI models to catch issues that a single model would miss.

## Why Multi-Model Review?

The same model that wrote the spec will tend to confirm its own work. Different models have different blind spots — cross-review catches structural issues, logical contradictions, and missing edge cases that single-model review misses.

## Instructions

### Step 1: Locate the Spec

If `$ARGUMENTS` is provided, use it as the spec directory path.
Otherwise, search for spec directories:

```bash
find . -path "*specs*" -name "requirements.md" -o -path "*specs*" -name "design.md" | head -20
```

Ask the user to confirm which spec to review.

### Step 2: Read All Spec Files

Read the following files from the spec directory:
- `requirements.md` (if exists)
- `design.md` (if exists)
- `tasks.md` (if exists)

If any file is missing, note it as a finding.

### Step 3: Self-Clarify (Quick Internal Check)

Before calling external models, do a quick consistency check:
- Does every requirement have a corresponding design section?
- Does every design component have corresponding tasks?
- Are task dependencies ordered correctly (no circular deps)?
- Are `[HUMAN]` tags used appropriately?

List any contradictions found.

### Step 4: Run Cross-Review Script

Execute the review script:

```bash
bash ${CLAUDE_SKILL_DIR}/../scripts/review-spec.sh "<spec-directory>"
```

This script:
1. Sends specs to a fresh Claude instance (zero shared context)
2. Sends specs to Gemini 3 Pro (different model, different blind spots)
3. Merges both reviews into a final report

### Step 5: Present Results

Read the generated `reviews/review-final.md` and present to the user:

1. Show **Critical** issues first (both reviewers agree)
2. Show **Major** issues (one reviewer flagged)
3. Show **Minor** suggestions
4. Highlight items marked as **Consensus** (highest confidence)

### Step 6: Offer Next Steps

Ask the user:
- Fix the Critical issues now?
- Re-run review after fixes?
- Proceed to pipeline execution?

## Review Checklist (Built into the Review Prompt)

### Spec Consistency
- Every requirement has a corresponding design module
- Every design module has corresponding tasks
- `[HUMAN]` tags are justified (not marking AI-doable tasks as HUMAN)

### Technical Risk
- External dependencies are specified (API versions, SDK versions)
- Performance risks identified (large data, concurrency, timeouts)
- Security risks addressed (permissions, data leaks, injection)

### Executability
- Every task has a testable acceptance criterion
- Task granularity fits single pipeline module execution
- Task dependency order is correct

### Completeness
- Error handling and edge cases covered
- Test tasks exist for each functional task
- Data migration / backward compatibility (if applicable)

---
> Source: [yinshucheng/claude-skills](https://github.com/yinshucheng/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
