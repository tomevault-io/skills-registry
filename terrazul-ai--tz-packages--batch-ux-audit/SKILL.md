---
name: batch-ux-audit
description: Comprehensive UX audit combining heuristics, accessibility, and flow testing across multiple pages Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# Batch UX Audit Skill

Coordinate comprehensive UX evaluations across multiple pages and evaluation types, synthesizing findings into actionable reports.

## When to Use

- Full site evaluation before launch
- Periodic UX health checks
- Competitive analysis
- Design system validation
- Multi-page feature audit

## Capabilities

1. **Multi-page evaluation** - Audit multiple URLs systematically
2. **Combined analysis** - Heuristics + Accessibility + Flow testing
3. **Pattern identification** - Find cross-page issues
4. **Global prioritization** - Rank issues across entire site
5. **Comprehensive reporting** - Synthesized findings

## Workflow

### Phase 1: Scope Definition

Clarify with user:
- Which pages to audit (URLs or site-wide crawl)
- Evaluation types (all, heuristics, accessibility, flows)
- Priority areas (key user journeys)
- WCAG conformance level required
- Report format preferences

### Phase 2: Page Discovery

Options:
- User provides specific URLs
- Crawl from homepage
- Focus on key user journeys
- Audit navigation structure

### Phase 3: Systematic Evaluation

For each page:

1. **Heuristics Evaluation**
   - All 10 Nielsen heuristics
   - Severity ratings (0-4)
   - Screenshot evidence

2. **Accessibility Audit**
   - WCAG criteria check
   - Keyboard testing
   - Contrast analysis

3. **Flow Testing** (where applicable)
   - Identify flows on page
   - Execute and evaluate
   - Note friction points

### Phase 4: Cross-Page Analysis

Identify patterns:
- **Consistent issues** - Same problem on multiple pages
- **Inconsistencies** - Elements vary between pages
- **Global vs local** - Site-wide vs page-specific issues
- **Systemic problems** - Design system gaps

### Phase 5: Prioritization

Create global priority matrix considering:
- Severity (0-4 scale)
- Reach (how many pages affected)
- User impact
- Business criticality
- Compliance requirements
- Fix effort

### Phase 6: Report Generation

Use **report-generator** skill for consistent output.

## Output Format

```markdown
# Comprehensive UX Audit Report

**Site**: [URL]
**Date**: [Date]
**Pages Audited**: [Count]

## Executive Summary

| Metric | Value |
|--------|-------|
| Overall UX Score | X/100 |
| Accessibility Status | [Status] |
| Pages Audited | X |
| Total Issues | X |
| Critical Issues | X |

### Top 5 Findings
1. [Finding 1]
2. [Finding 2]
3. [Finding 3]
4. [Finding 4]
5. [Finding 5]

### Critical Actions Required
1. [Action 1]
2. [Action 2]

## Audit Coverage

| Page | Heuristics | Accessibility | Flows |
|------|------------|---------------|-------|
| Homepage | ✓ | ✓ | - |
| Signup | ✓ | ✓ | ✓ |
| Dashboard | ✓ | ✓ | - |

## Cross-Page Patterns

### Pattern 1: [Pattern Name]
- **Affected Pages**: [List]
- **Issue**: [Description]
- **Impact**: [User impact]
- **Recommendation**: [Fix]

## Per-Page Summaries

### Homepage
- UX Score: X/100
- Critical Issues: X
- [Key findings]

### Signup Page
- UX Score: X/100
- Critical Issues: X
- [Key findings]

[Continue for each page...]

## Priority Matrix

### Immediate (Critical + Easy)
1. [Issue] - [Pages] - Effort: Low

### Short-term (High Impact)
1. [Issue] - [Pages] - Effort: Medium

### Medium-term (Systemic)
1. [Issue] - Site-wide - Effort: High

## Remediation Roadmap

### Week 1-2
- [ ] [Fix critical issues]

### Week 3-4
- [ ] [Address high priority]

### Month 2+
- [ ] [Systemic improvements]
```

## Best Practices

1. **Scope appropriately** - Don't audit everything at once
2. **Prioritize ruthlessly** - Focus on high-impact pages
3. **Be consistent** - Apply same criteria across pages
4. **Document patterns** - Not just individual issues
5. **Provide roadmap** - Actionable remediation plan

## Example Usage

```
Use the batch-ux-audit skill to evaluate:
- Homepage
- Signup flow
- Main dashboard
- Settings page

Focus on heuristics and accessibility at AA level.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
