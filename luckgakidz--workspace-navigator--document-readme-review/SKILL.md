---
name: document-readme-review
description: Review an existing README.md against best practices and produce improvement suggestions. Evaluate with a checklist across required items, quality, and style, and output prioritized recommendations. Use when asked to "review README", "check README", "find README improvements", or review an existing README.md. Use when this capability is needed.
metadata:
  author: luckgakidz
---

# README.md Review Skill

This skill systematically reviews an existing README.md against best practices and generates concrete improvement suggestions.

## When to Use This Skill

- Evaluating quality of an existing README.md
- Identifying improvements in README.md
- Confirming README.md quality before PR/MR
- Verifying consistency after applying `document.readme.update`

## Prerequisites

- `README.md` exists in the project root
- Recommended: current project state already analyzed via `document.readme.analyze`

## Step-by-Step Workflows

### Workflow 1: Full Review

1. **Understand current project state**
   - Analyze latest state with `document.readme.analyze`
   - Collect tech stack, directory structure, script list

2. **Load existing README**
   - Read full `README.md` with `#readFile`
   - Organize section structure (h1/h2/h3)

3. **Checklist-based evaluation**
   - Evaluate all items in the checklist below in order
   - Mark each as ✅ PASS / ❌ FAIL / ⚠️ WARN
   - For FAIL/WARN, record concrete reasons and improvements

4. **Compare with actual project state**
   - Compare analysis results vs README statements
   - Identify outdated/inaccurate sections
   - Find missing information (new dependencies, scripts, etc.)

5. **Generate improvement proposals**
   - Format output following the review template
   - Add priority (high/medium/low)

### Workflow 2: Spot Review by Section

1. Read target section with `#readFile`
2. Evaluate only related checklist items
3. Output improvement suggestions

### Workflow 3: Post-Update Validation

1. Read updated README.md from `document.readme.update`
2. Check required-items checklist for gaps
3. Validate consistency with style checklist
4. Output fixes if issues exist

## Review Checklist

### Required Items

Minimum items README.md should include:

| # | Check Item | Verification Point |
|---|------------|--------------------|
| 1 | Project name | Clear project name in h1 |
| 2 | Description | Purpose/value expressed in 1-3 sentences |
| 3 | Installation steps | Setup commands provided in code blocks |
| 4 | Basic usage | Includes code or command examples |
| 5 | License info | Mentions LICENSE file |

### Quality Items

Items ensuring documentation quality:

| # | Check Item | Verification Point |
|---|------------|--------------------|
| 1 | Heading structure | Logical h1 -> h2 -> h3 hierarchy, no level skipping |
| 2 | Code example accuracy | Copy-pasteable and actually runnable |
| 3 | Link validity | Internal/external links are not broken |
| 4 | Badge accuracy | Badge URLs are correct and status is current |
| 5 | Table of contents | Included when there are 5+ sections |
| 6 | Env vars/config | Variables listed/described when used |
| 7 | Contributing | Contribution guide/link exists |

### Style Items

Consistency of markdown writing style:

| # | Check Item | Verification Point |
|---|------------|--------------------|
| 1 | Markdown syntax | No syntax errors (unclosed code blocks, broken tables, etc.) |
| 2 | Blank lines | Proper spacing around headings |
| 3 | Code blocks | Language specified for all blocks (```bash, ```ts, etc.) |
| 4 | List style | Bullet marker style is consistent (`-`, `*`, `+`) |
| 5 | Emoji consistency | If emojis are used, use them consistently across sections |

## Review Result Format

Output review results in this structure:

```markdown
## README.md Review Results

### Score Summary

| Category | PASS | WARN | FAIL | Score |
|----------|:----:|:----:|:----:|:-----:|
| Required Items | 4 | 0 | 1 | 80% |
| Quality Items | 5 | 1 | 1 | 71% |
| Style Items | 4 | 1 | 0 | 80% |
| **Overall** | **13** | **2** | **2** | **76%** |

### Improvement Proposals (Priority Order)

#### 🔴 Priority: High (FAIL)
1. **[Required] Missing installation steps**
   - Current: No installation section
   - Proposal: Add `## Installation` section with `npm install`
   - Location: -(new section)

#### 🟡 Priority: Medium (WARN)
1. **[Quality] Missing table of contents**
   - Current: 8 sections but no TOC
   - Proposal: Add `## Table of Contents` right after badges

#### 🟢 Priority: Low (recommended improvement)
1. **[Style] Missing code block language tags**
   - Current: 3 code blocks missing language spec
   - Proposal: Add appropriate languages (bash, typescript, etc.)
```

## Scoring Guidelines

### Score Calculation

- **PASS**: 1 point
- **WARN**: 0.5 points
- **FAIL**: 0 points
- Score = (earned points / max points) x 100%

### Priority Criteria

| Priority | Criteria |
|----------|----------|
| 🔴 High | FAIL in required items; prevents users from getting started |
| 🟡 Medium | FAIL in quality items or WARN in required items; impacts UX |
| 🟢 Low | FAIL/WARN in style items or WARN in quality items; improvement recommended |

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No analysis data | Run `document.readme.analyze` first |
| README is very long | Use section-level spot review (Workflow 2) |
| README deviates heavily from project state | Propose full rewrite with `document.readme.generate` |
| Criteria feel too strict | Adjust WARN/FAIL thresholds by project size/maturity |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luckgakidz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
