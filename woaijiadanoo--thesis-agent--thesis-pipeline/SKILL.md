---
name: thesis-pipeline
description: | Use when this capability is needed.
metadata:
  author: woaijiadanoo
---

# Thesis Pipeline: Complete Chapter Processing

Orchestrate all thesis proofreading skills for comprehensive chapter revision.

## Your Task

When processing a thesis chapter:

1. **Apply LaTeX Guard** - Protect all structural elements
2. **Apply Academic Polisher** - Convert to formal academic style
3. **Apply Humanizer** (optional) - Reduce AI writing patterns
4. **Address Supervisor Feedback** - If comments provided
5. **Ensure Consistency** - Use terminology reference if provided
6. **Output Complete LaTeX** - Return compilable code

## Pipeline Execution Order

```
Input: LaTeX Chapter + (optional) Supervisor Comments + (optional) Terminology Reference
                                    │
                                    ▼
                    ┌───────────────────────────────┐
                    │      1. LATEX-GUARD           │
                    │   Protect citations, refs,    │
                    │   figures, math, paths        │
                    └───────────────────────────────┘
                                    │
                                    ▼
                    ┌───────────────────────────────┐
                    │    2. ACADEMIC-POLISHER       │
                    │   Passive voice, 3+ sentences │
                    │   per paragraph, formal vocab │
                    └───────────────────────────────┘
                                    │
                                    ▼
                    ┌───────────────────────────────┐
                    │    3. HUMANIZER (optional)    │
                    │   Reduce AI patterns while    │
                    │   keeping academic tone       │
                    └───────────────────────────────┘
                                    │
                                    ▼
                           Output: Polished LaTeX
```

## Processing Supervisor Comments

When supervisor feedback is provided:

### Step 1: Parse Comments

Identify specific requests:
- "Expand discussion on X"
- "Add more detail to Section Y"
- "Clarify the methodology"
- "Strengthen the argument for Z"

### Step 2: Address Each Comment

For expansion requests:
- Add relevant supporting information
- Ensure new content follows academic style
- Maintain minimum 3 sentences per paragraph

For clarification requests:
- Restructure existing content for clarity
- Add transitional sentences if needed

### Step 3: Verify Addressing

Check each supervisor comment was addressed before returning.

## Terminology Reference Usage

When a terminology reference file is provided via `@filename`:

### Check Abbreviations

- Use abbreviation only after first definition
- If chapter uses abbreviation without definition, add full form
- Example: "CNN" → "Convolutional Neural Network (CNN)" on first use

### Maintain Consistency

- Use exact spelling/capitalization from reference
- Apply consistent hyphenation rules
- Follow chapter-specific notes

### Template

Users can create their own reference using [references/terminology-template.md](references/terminology-template.md).

## Cross-Chapter Consistency

Even without a terminology file:

- Note any abbreviations used - are they defined in this chapter?
- Flag potential inconsistencies to user
- Suggest checking previous chapters for definitions

## Humanizer Academic Mode

When applying humanizer to thesis content:

**Override default humanizer behavior**:
- Maintain passive voice (don't add "I")
- Keep formal academic tone
- Focus only on:
  - Sentence structure variation
  - Removing formulaic transitions
  - Eliminating AI vocabulary words
  - Reducing filler phrases

See [references/humanizer-integration.md](references/humanizer-integration.md) for details.

## Edge Cases

### Malformed Citations

- Preserve exactly as-is
- User will verify in Overleaf
- Note any concerns in output

### Nested Environments

- Protect entire outer environment
- No editing inside figure/table blocks

### Minimal Editable Content

- If chapter is mostly figures/tables/equations
- Return with minimal prose changes
- Note which areas were editable

### No Supervisor Comments

- Apply standard pipeline without expansion
- Focus on style and tone improvements

### Conflicting Requirements

If supervisor feedback conflicts with academic style:
- Prioritize supervisor request
- Note the conflict to user
- Example: If supervisor says "use first person" - follow supervisor

## Output Format

Return complete, compilable LaTeX:

```latex
\chapter{Results and Discussion}
\label{ch:results}

\section{Experimental Results}
\label{sec:exp_results}

The experimental results demonstrate the effectiveness of the 
proposed methodology. As illustrated in Figure~\ref{fig:results}, 
the classification accuracy achieved 94.5\% on the benchmark 
dataset \citep{smith2023}. These findings align with previous 
research conducted by \citet{wang2021}, who reported similar 
performance improvements.

The analysis reveals three significant patterns in the data. 
First, the preprocessing stage contributed substantially to 
the overall accuracy improvement. Second, the feature extraction 
method outperformed traditional approaches. Third, the proposed 
architecture demonstrated robust generalization capabilities 
across different test scenarios.
```

## Process Summary

1. **Read** chapter content and any provided references
2. **Identify** all LaTeX structural elements (latex-guard)
3. **Convert** first-person to passive voice (academic-polisher)
4. **Ensure** 3+ sentences per paragraph (academic-polisher)
5. **Upgrade** vocabulary to formal academic (academic-polisher)
6. **Reduce** AI patterns if requested (humanizer)
7. **Address** supervisor comments if provided
8. **Check** terminology consistency if reference provided
9. **Verify** all protected elements unchanged
10. **Return** complete, compilable LaTeX code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/woaijiadanoo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
