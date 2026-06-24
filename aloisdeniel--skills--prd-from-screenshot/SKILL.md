---
name: prd-from-screenshot
description: Analyze product screenshots to extract feature lists and generate development task checklists. Use when: (1) Analyzing competitor product screenshots for feature extraction, (2) Generating PRD/task lists from UI designs, (3) Batch analyzing multiple app screens, (4) Conducting competitive analysis from visual references. Use when this capability is needed.
metadata:
  author: aloisdeniel
---

# Screenshot Analyzer (Multi-Agent)

Extract product features from UI screenshots using a coordinated multi-agent analysis pipeline.

**Core principle**: Describe WHAT to build (features/interactions), NOT HOW (no tech stack).

## Multi-Agent Architecture

This skill orchestrates 5 specialized agents for comprehensive analysis:

```
                    ┌─────────────────┐
                    │   Coordinator   │
                    │   (this skill)  │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
         ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│  UI Analyzer    │ │  Interaction    │ │   Business      │
│  (parallel)     │ │   Analyzer      │ │    Analyzer     │
│                 │ │  (parallel)     │ │   (parallel)    │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │
         └───────────────────┼───────────────────┘
                             ▼
                    ┌─────────────────┐
                    │   Synthesizer   │
                    │   (sequential)  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    Reviewer     │
                    │   (sequential)  │
                    └─────────────────┘
```

## Process

### Phase 1: Screenshot Collection

Gather all screenshots to analyze:
1. Read the screenshot file(s) provided by the user
2. For each screenshot, note the file path and any context provided
3. If multiple screenshots, determine if they are from the same product

### Phase 2: Parallel Analysis

Launch THREE Task agents IN PARALLEL for each screenshot:

**Agent 1: screenshot-ui-analyzer**
```
Analyze this screenshot for UI components, layout structure, and design patterns.
Screenshot: [file path]
Return your analysis as JSON.
```

**Agent 2: screenshot-interaction-analyzer**
```
Analyze this screenshot for user interactions, navigation flows, and state transitions.
Screenshot: [file path]
Return your analysis as JSON.
```

**Agent 3: screenshot-business-analyzer**
```
Analyze this screenshot for business functions, data entities, and domain logic.
Screenshot: [file path]
Return your analysis as JSON.
```

**IMPORTANT**: Use the Task tool with THREE parallel calls in a single message to maximize efficiency.

### Phase 3: Synthesis

After all parallel analyses complete, launch the synthesizer agent:

**Agent 4: screenshot-synthesizer**
```
Synthesize these analysis results into a unified development task list.

UI Analysis:
[paste UI analyzer result]

Interaction Analysis:
[paste Interaction analyzer result]

Business Analysis:
[paste Business analyzer result]

Product Name: [product name]
Output file: docs/plans/YYYY-MM-DD-<product>-features.md
```

### Phase 4: Review

Launch the reviewer agent to validate the output:

**Agent 5: screenshot-reviewer**
```
Review this task list for completeness and quality.

Original screenshot(s): [file paths]
Task list: [synthesized output]

If issues found, provide corrections.
```

### Phase 5: Output

Use the `prd` skill to create a new feature. 

Complete the new PRD document located in `prd/<new-feature-id>/prd.md` from the results of your analysis.

---

#### Checklist

Before saving the PRD:

- [ ] Created a feature with `prd feat new`
- [ ] Asked clarifying questions with lettered options
- [ ] Incorporated user's answers
- [ ] User stories are small and specific
- [ ] Functional requirements are numbered and unambiguous
- [ ] Non-goals section defines clear boundaries
- [ ] Updated the `prd/<feature>/prd.md` document

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aloisdeniel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
