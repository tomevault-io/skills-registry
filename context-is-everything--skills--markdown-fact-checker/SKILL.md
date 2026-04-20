---
name: markdown-fact-checker
description: >- Use when this capability is needed.
metadata:
  author: context-is-everything
---

# 🔍 Markdown Fact Checker

**Created**: 2026-01-28
**Purpose**: Self-audit tool to detect hallucinations in Claude-generated markdown
**Audience**: Claude performing self-QA or consultant reviewing documents
**Status**: Active

---

## 🎯 Purpose & Scope

### What This Skill Covers

This skill helps detect hallucinations and verify factual accuracy in markdown documents by systematically checking:

- **URL Verification**: Existence and content matching claims
- **Quote Verification**: Existence in source files and context preservation
- **Statistical Claims**: Number accuracy and proper attribution
- **Names/Organizations**: Spelling consistency and correct attribution
- **Dates/Timelines**: Chronological accuracy and consistency
- **Source Attribution**: Proper citation and provenance tracking

### What This Does NOT Cover

- Writing quality or style assessment
- Document formatting or structure
- Content completeness or comprehensiveness
- Argument strength or logical validity
- Opinion or analysis evaluation

### When to Use

**Primary Use Case**: After completing a research document, before delivering to user or client

**Trigger Scenarios**:
- Just finished writing research document with external sources
- Document contains quotes from transcripts or interviews
- Multiple URLs or statistics referenced
- Client-facing deliverable requiring high accuracy
- Any time factual claims need verification

---

## 📋 Prerequisites

Before starting fact check:

- ✅ **Document Path**: Full path to markdown file to audit
- ✅ **Source Access**: Referenced files and URLs must be accessible
- ✅ **Document Purpose**: Understand what the document claims to be (research report, EA summary, analysis, etc.)
- ✅ **Scope Definition**: Decide on full audit vs. rapid spot-check

**Why This Matters**: Cannot verify accuracy without access to claimed sources. Scope definition prevents wasted effort on low-risk sections.

---

## 🚀 Quick Start: 15-Minute Rapid Audit

Use this abbreviated process when time is limited or for quick pre-delivery checks.

### Tier 1: Critical Checks (7 minutes)

**Focus**: High-risk claim types that are commonly hallucinated

- [ ] **All URLs Load**: Use WebFetch to verify each URL returns 200 (not 404)
- [ ] **Major Factual Claims Have Sources**: Check that key statistics, quotes, and claims cite sources
- [ ] **Statistics Have Attribution**: Numbers reference specific sources or documents

**Stop condition**: If 3+ critical issues found, escalate to full audit

### Tier 2: Quote Verification (5 minutes)

**Focus**: Spot-check highest-impact quotes

- [ ] **Select 3-5 Key Quotes**: Pick quotes that are central to document's argument
- [ ] **Verify in Source Files**: Use Read or Grep to find exact or near-exact matches
- [ ] **Check Context**: Read surrounding text to ensure context preserved

**Red flags**: Quotes not found, paraphrased but presented as direct quotes, context contradicts usage

### Tier 3: Cross-References (3 minutes)

**Focus**: Internal consistency

- [ ] **Names/Organizations Spelled Correctly**: Check consistency throughout document
- [ ] **Dates Are Consistent**: Timeline makes logical sense
- [ ] **Cross-Document Claims Match**: If document references other documents, spot-check alignment

### If Issues Found

**Decision Point**:
- 0-1 issues → Fix and proceed
- 2-3 issues → Fix and consider full audit of similar claims
- 4+ issues → Run full audit using detailed procedures

---

## 🔍 The Fact Checking Process

### Overview: Four-Stage Verification

The complete fact-checking process follows four stages:

**Stage 1: Claim Extraction**
- Identify all factual claims in document
- Categorize by type (URL, quote, statistic, name, date, specification)
- Create claim inventory for systematic verification
- **See**: [references/verification-procedures.md](references/verification-procedures.md#stage-1-claim-extraction)

**Stage 2: Source Identification**
- Determine claimed provenance for each fact
- Map citations to source documents or URLs
- Flag unsourced claims that should have attribution
- Verify source accessibility before verification attempts
- **See**: [references/verification-procedures.md](references/verification-procedures.md#stage-2-source-identification)

**Stage 3: Verification**
- Check each claim against its source using appropriate tool
- Document findings (verified/false/uncertain)
- Assign confidence scores based on match quality
- Distinguish between false claims and unverifiable claims
- **See**: [references/verification-procedures.md](references/verification-procedures.md#stage-3-verification)

**Stage 4: Report Generation**
- Organize findings by severity (CRITICAL/IMPORTANT/MINOR/FALSE POSITIVE)
- Document all issues with specific recommendations
- Provide actionable next steps for document improvement
- **See**: [references/output-template.md](references/output-template.md)

---

## 📋 Claim Categories

### 1. URLs and Web References

**Risk Level**: 🔴 HIGH - Claude frequently invents plausible-sounding URLs

**What to Check**:
- URL exists (returns 200, not 404)
- Content on page matches description in document
- Path is correct (not just domain)

**Verification Tool**: WebFetch

**Common Issues**:
- Invented but plausible URLs (e.g., "company.com/about/team" when page doesn't exist)
- Correct domain, wrong path
- Outdated URLs from training data

**See**: [references/hallucination-types.md](references/hallucination-types.md#url-hallucinations)

---

### 2. Quotes from Files

**Risk Level**: 🔴 HIGH - May paraphrase vs. quote, misattribute, or invent

**What to Check**:
- Exact or near-exact match exists in source file
- Attribution correct (right person/document)
- Context preserved (quote not taken out of context)
- Direct quotes vs. acceptable paraphrasing

**Verification Tools**: Read + Grep

**Common Issues**:
- Paraphrasing presented as direct quotes
- Composite quotes (combining multiple statements)
- Invented quotes with no source match
- Context changes meaning

**See**: [references/hallucination-types.md](references/hallucination-types.md#quote-hallucinations)

---

### 3. Statistics and Metrics

**Risk Level**: 🟡 MEDIUM - May misremember numbers or round incorrectly

**What to Check**:
- Number matches source exactly (or with disclosed rounding)
- Units correct (%, $, thousands vs. millions)
- Context matches (same time period, same metric)
- Attribution present

**Verification Tools**: Read + Grep

**Common Issues**:
- Transposed digits (1,450 vs. 1,540)
- Wrong magnitude ($1.5M vs. $1.5B)
- Undisclosed rounding (47.3% → "50%")
- Wrong units or context

**See**: [references/hallucination-types.md](references/hallucination-types.md#statistical-hallucinations)

---

### 4. Names and Organizations

**Risk Level**: 🟡 MEDIUM - May misspell or confuse similar entities

**What to Check**:
- Spelling consistent throughout document
- Same entity (not similar name of different entity)
- Titles/roles correct
- Attribution accurate

**Verification Tool**: Grep (for consistency checking)

**Common Issues**:
- Similar company names confused (Acme Corp vs. Acme Technologies)
- Title errors (CEO vs. President)
- Inconsistent spelling variations

**See**: [references/hallucination-types.md](references/hallucination-types.md#name-organization-hallucinations)

---

### 5. Dates and Timelines

**Risk Level**: 🟡 MEDIUM - May transpose years or miscalculate sequences

**What to Check**:
- Dates match sources
- Timeline logic correct (sequences, "before"/"after" relationships)
- Consistency across document

**Verification Tools**: Read + Grep

**Common Issues**:
- Year transposition (2023 vs. 2024)
- Sequence errors (chronology reversed)
- Inconsistent dates for same event

**See**: [references/hallucination-types.md](references/hallucination-types.md#date-timeline-hallucinations)

---

### 6. Technical Specifications

**Risk Level**: 🟢 LOW - Usually copied correctly, but verify critical specs

**What to Check**:
- Specifications match source documentation
- Technical accuracy for critical specs
- Version numbers correct

**Verification Tool**: Read

**Common Issues**:
- Outdated specifications from training data
- Misremembered technical details

---

**Complete Catalog**: See [references/hallucination-types.md](references/hallucination-types.md) for exhaustive list of hallucination patterns

---

## 🔧 Verification Tools

### When to Use Which Tool

**WebFetch** - For URL and web content verification

**Use When**: Document references external websites or online resources

**Verifies**:
- URL exists and is accessible
- Page content matches claim about what the page says
- Links are current (not broken)

**Example Pattern**:
```
WebFetch url="https://example.com/page" prompt="Does this URL exist and load successfully?"
WebFetch url="https://example.com/page" prompt="Does this page mention [specific claim]? Quote the relevant section."
```

---

**Read** - For file content verification

**Use When**: Document quotes or cites local files (transcripts, reports, other markdown files)

**Verifies**:
- File exists and is accessible
- File contains claimed content
- Context around quote/claim

**Example Pattern**:
```
Read file_path="/path/to/source-document.md"
[Then manually search output for claimed content]
```

---

**Grep** - For searching specific text/phrases

**Use When**: Need to find exact quotes or specific text patterns across files

**Verifies**:
- Exact phrase exists in source
- How many times phrase appears
- Context around matching text

**Example Pattern**:
```
Grep pattern="exact quote text" path="/path/to/source.md" output_mode="content"
Grep pattern="key phrase" path="/directory/" output_mode="files_with_matches"
```

**Tips**:
- Use `-C=2` flag to see context (2 lines before/after)
- Start with exact phrase, then try key words if no match
- Use `files_with_matches` mode to find which files contain text

---

**Glob** - For file discovery and pattern matching

**Use When**: Need to find files referenced by description or pattern

**Verifies**:
- Files matching description exist
- File naming patterns correct

**Example Pattern**:
```
Glob pattern="**/*keyword*.md" path="/base/directory"
Glob pattern="2024-*-report.md" path="/reports/"
```

---

**Detailed Procedures**: See [references/verification-procedures.md](references/verification-procedures.md) for step-by-step instructions for each tool

---

## 📊 Confidence Scoring Framework

### Scoring Rubric Overview

Every verified claim receives a confidence score reflecting certainty that the claim is accurate.

**Scoring Range**: 0-100% or N/A (unverifiable)

### The Four Confidence Tiers

**90-100% (✅ VERIFIED)**
- Direct match found in source
- Context fully preserved
- Attribution correct
- Reliable, authoritative source
- Multiple confirmations (if available)

**Example**: Exact quote found word-for-word in transcript with proper context

---

**50-89% (⚠️ QUESTIONABLE)**
- Partial match or acceptable paraphrase
- Source somewhat ambiguous
- Minor discrepancies present
- Single source confirmation
- Context mostly preserved

**Example**: Quote closely paraphrased, meaning intact but not exact words

---

**0-49% (❌ LIKELY FALSE)**
- No match in claimed source
- Contradicts source
- Source doesn't exist (404, file not found)
- Context significantly misrepresented

**Example**: Statistic doesn't match source number, or URL returns 404

---

**N/A (❓ UNVERIFIABLE)**
- Source not accessible (requires login, behind paywall)
- Claim too vague to verify
- Insufficient information provided
- No source cited for verifiable claim

**Example**: "Studies show..." with no citation, or URL requires authentication

---

### Factors Affecting Confidence Score

**Increases Confidence** (+):
- Source is primary/authoritative (+)
- Exact match found (+)
- Context perfectly preserved (+)
- Multiple independent sources confirm (+)
- Cross-references validate (+)

**Decreases Confidence** (-):
- Source reliability questionable (-)
- Only partial match (-)
- Context differs or unclear (-)
- Single source with no cross-reference (-)
- Contradictory information found (-)

---

**Complete Rubric**: See [references/confidence-scoring.md](references/confidence-scoring.md) for detailed scoring methodology and examples

---

## 📝 Audit Report Format

### Severity Classification System

All issues are classified by severity to prioritize fixes:

**🔴 CRITICAL** - Factually incorrect, must fix before delivery
- Fake URLs (404 errors)
- Invented quotes (no source match)
- Wrong statistics or numbers
- Misattributed claims
- Wrong entity names (different companies)

**🟡 IMPORTANT** - Questionable accuracy, should fix for quality
- Paraphrases presented as direct quotes
- Missing sources for verifiable claims
- Context not fully preserved
- Ambiguous attributions
- Rounding without qualifiers

**🟢 MINOR** - Minor issues, fix if time allows
- Formatting inconsistencies
- Acceptable variations (e.g., "Corp" vs. "Corporation")
- Minor spelling variations of same entity

**⚪ FALSE POSITIVE** - Looks wrong but is actually acceptable
- Quotelization (filler words removed, meaning preserved)
- Reasonable rounding WITH qualifier ("approximately")
- Standard abbreviations (CEO vs. Chief Executive Officer)
- URL format variations (trailing slash, www vs. non-www)

---

### Report Structure

Every audit produces a structured report with **problems surfaced first**:

1. **Executive Summary**: Critical/Important/Minor issue counts and document readiness
2. **Detailed Findings by Severity**: CRITICAL, IMPORTANT, MINOR issues with evidence and fixes
3. **Unverifiable Claims**: Missing sources or inaccessible content
4. **Verified Claims by Category**: URLs, quotes, statistics, etc. (for reference)
5. **False Positives**: Acceptable variations documented
6. **Verification Coverage**: Analysis of what was checked
7. **Recommendations**: Priority actions and systematic improvements

**Philosophy**: Users care most about what's WRONG, not what's right. Issues get top billing.

---

**Report Template**: See [references/output-template.md](references/output-template.md) for complete template with examples

---

## 💡 Best Practices

### During Verification

1. **Verify Systematically** - Don't skip claim categories; follow process
2. **Document Uncertainties** - Flag unclear cases rather than guessing
3. **Check Verifiable Claims First** - URLs and quotes before subjective claims
4. **Separate Unverifiable from False** - Different categories, different implications
5. **Be Honest About Confidence** - Better to flag uncertainty than assume correctness
6. **Check "Obvious" Claims** - Common knowledge can be wrong
7. **Track False Positives** - Build pattern recognition over time

### Report Writing

8. **Be Specific in Recommendations** - "Fix URL to..." not "Fix the URL"
9. **Provide Evidence** - Quote relevant verification output
10. **Explain Severity Choices** - Why is this CRITICAL vs. IMPORTANT?
11. **Note Patterns** - Multiple similar errors suggest systematic issue
12. **Distinguish Can't Verify from Wrong** - Unverifiable ≠ false

### Efficiency

13. **Use Glob Before Read** - Find files first, then read
14. **Use Grep for Specific Searches** - Don't read entire files unnecessarily
15. **WebFetch with Focused Prompts** - Ask specific questions
16. **Stop Rapid Audit Early** - If issues found, escalate to full audit

---

## 🚨 Common Pitfalls to Avoid

### False Positives vs. Real Errors

**Pitfall**: Marking acceptable variations as errors

**Example**: Flagging "approximately 50%" when source says "47.3%" (this is acceptable)

**Solution**: Review [references/false-positives.md](references/false-positives.md) before finalizing report

---

### Verification Scope Creep

**Pitfall**: Starting to verify related claims beyond original scope

**Example**: Auditing document about Company A, then verifying claims about Company B mentioned in passing

**Solution**: Define clear scope boundaries before starting; note out-of-scope items for separate review

---

### Unverifiable = False

**Pitfall**: Marking claims as "false" when they're actually just unverifiable

**Example**: Flagging "Industry analysts estimate..." as false because no source cited (should be "unverifiable")

**Solution**: Use N/A category for claims that cannot be checked, not 0% confidence

---

### Ignoring Context

**Pitfall**: Verifying quote text without checking surrounding context

**Example**: Quote is word-for-word accurate but used to support opposite point

**Solution**: Always read ±3 sentences around quote for context verification

---

### Over-Confidence in Scoring

**Pitfall**: Assigning high confidence scores too generously

**Example**: Giving 95% confidence to paraphrased quote (should be 70-85%)

**Solution**: Use conservative scoring; better to under-promise and over-deliver

---

## 🔄 Related Skills

- **creating-guides** - Documentation creation standards
- **beautiful-documentation-design** - Document quality guidelines
- **research-digital-investigation** - Source gathering methods
- **Aesop Standards-ea-report-quality-review** - EA report QA process (complementary audit)
- **Aesop Standards-ea-quote-sorting** - Quote verification for EA work

---

## 📚 Reference Files

### Detailed Procedures

- **[references/verification-procedures.md](references/verification-procedures.md)** - Complete step-by-step verification process for all four stages
- **[references/confidence-scoring.md](references/confidence-scoring.md)** - Detailed scoring rubric with calibration examples

### Supporting Resources

- **[references/hallucination-types.md](references/hallucination-types.md)** - Comprehensive catalog of common hallucination patterns
- **[references/false-positives.md](references/false-positives.md)** - Guide to acceptable variations that look like errors
- **[references/examples.md](references/examples.md)** - Real audit examples with complete verification trails
- **[references/output-template.md](references/output-template.md)** - Standard audit report template

### How to Use Reference Files

**During Claim Extraction**: Reference `hallucination-types.md` to recognize patterns

**During Verification**: Follow step-by-step procedures in `verification-procedures.md`

**During Scoring**: Use rubric in `confidence-scoring.md` for consistent assessment

**Before Finalizing Report**: Check `false-positives.md` to avoid over-flagging

**For Report Format**: Follow structure in `output-template.md`

**For Examples**: Review `examples.md` to see complete verification processes

---

## 🎯 Success Criteria

This skill succeeds when:

- **Accuracy Improved**: Documents have fewer factual errors after fact-checking
- **Confidence Calibrated**: High confidence scores (90%+) consistently indicate accurate claims
- **Issues Prioritized**: CRITICAL issues are truly critical, not over-flagged
- **Reports Actionable**: Recommendations are specific enough to implement
- **Efficiency Gained**: Rapid audits catch issues in 15 minutes; full audits provide comprehensive coverage
- **False Positives Minimized**: Acceptable variations are correctly identified, not flagged as errors

---

## 📝 Quick Reference Card

### Claim Type → Tool Mapping

| Claim Type | Primary Tool | Verification Focus |
|------------|--------------|-------------------|
| URLs | WebFetch | Exists + content matches |
| Quotes | Read/Grep | Exact/near match + context |
| Statistics | Read/Grep | Number + units + context |
| Names | Grep | Spelling consistency |
| Dates | Read/Grep | Accuracy + timeline logic |

### Confidence Score Quick Guide

- 90-100%: Exact match, verified
- 50-89%: Close match, questionable
- 0-49%: No match, likely false
- N/A: Cannot verify

### Severity Quick Guide

- 🔴 CRITICAL: Wrong facts, fake URLs, invented quotes
- 🟡 IMPORTANT: Missing sources, questionable accuracy
- 🟢 MINOR: Formatting, minor variations
- ⚪ FALSE POSITIVE: Looks wrong but acceptable

---

**Last Updated**: 2026-01-28
**Version**: 1.0
**Maintainer**: Sasha Studio Knowledge Management

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/context-is-everything) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
