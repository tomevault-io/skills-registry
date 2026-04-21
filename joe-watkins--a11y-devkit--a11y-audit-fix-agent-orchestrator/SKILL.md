---
name: a11y-audit-fix-agent-orchestrator
description: Coordinate a full accessibility audit workflow across multiple a11y skills. Use this skill when users request "full accessibility audit", "accessibility audit", "remediate accessibility", "fix accessibility issues", "a11y audit", "test and fix accessibility", or any comprehensive accessibility analysis and remediation of code or URLs. This skill manages the three-stage workflow (analysis → remediation → validation) and produces a complete remediation report. Use when this capability is needed.
metadata:
  author: joe-watkins
---

# Accessibility Orchestrator

Coordinates the complete accessibility audit workflow by directing AI to use specialized a11y skills in sequence.

## CRITICAL: Autonomous Execution

**This workflow runs autonomously without stopping.** Do NOT:
- Stop to ask "Would you like me to apply fixes?"
- Pause between stages for confirmation
- Request permission before making changes
- Break the workflow into multiple interactions

**Execute all three stages in a single continuous run.** The user has already requested an audit—that is your authorization to complete the entire workflow.

```
┌─────────────────────────────────────────────────────────────┐
│                    AUTONOMOUS WORKFLOW                       │
│              No human-in-the-loop required                  │
└─────────────────────────────────────────────────────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
   ┌───────────┐    ┌───────────┐    ┌───────────┐
   │  Stage 1  │───►│  Stage 2  │───►│  Stage 3  │
   │ Analysis  │    │Remediation│    │Validation │
   └───────────┘    └───────────┘    └───────────┘
         │                │                │
   Static → Auto     Compare &        Re-test with
   then compare     apply fixes       axe-core again
```

### Analysis Flow

```
┌─────────────────────────────────────────────────────────────┐
│  1. STATIC ANALYSIS (web-standards) ←── Initial Scan        │
│     - Catch structural issues early                         │
│     - Identify patterns needing attention                   │
│     - Build context for automated testing                   │
├─────────────────────────────────────────────────────────────┤
│  2. AUTOMATED TESTING (axe-core) ←── Runtime Validation     │
│     - Violations = confirmed issues                         │
│     - Incomplete = needs manual review                      │
│     - Tests actual rendered state                           │
├─────────────────────────────────────────────────────────────┤
│  3. COMPARE RESULTS ←── Determine Strategy                  │
│     - Both agree = high confidence, auto-fix                │
│     - Only axe-core = runtime issue, auto-fix               │
│     - Only static = potential issue, report for review      │
└─────────────────────────────────────────────────────────────┘
```

## Prerequisites

Before running an audit, ensure the `a11y-base-web` skill has been loaded. It provides foundational patterns all other skills rely on.

## Stage 1: Analysis

Run static analysis first to identify potential issues, then validate with automated testing, then compare results to plan remediation.

### Step 1A: Static Analysis (INITIAL SCAN)

**Start with static code analysis** using the `web-standards` skill to catch structural issues early:
- Missing or improper ARIA attributes
- Semantic HTML problems (div soup, missing landmarks)
- Keyboard accessibility patterns in custom widgets
- Form labeling issues
- Image accessibility

This gives you a preliminary list of potential issues and builds context for what to look for in automated testing.

### Step 1B: Automated Testing (RUNTIME VALIDATION)

**Next, run axe-core via Playwright** to validate against the actual rendered page. Read and apply the `a11y-tester` skill:

1. Start a local server if testing a local file (`python3 -m http.server`)
2. Run the `a11y-tester` skill against the target URL (or local server URL)
3. Collect the `violations` and `incomplete` arrays from results

Do not call low-level browser tools directly from the orchestrator unless `a11y-tester` is unavailable.

**Note:** The a11y-tester skill returns raw violations when called by the orchestrator. Issue formatting is handled separately by the a11y-issue-writer skill if needed.

Automated testing catches issues static analysis cannot:
- Computed accessible names
- Actual color contrast with rendered colors
- Focus management and live region behavior
- Dynamic content accessibility

### Step 1C: Compare Results & Plan Remediation

### Always Produce a Manual Review Queue (Layered Testing)

Even in an autonomous workflow, some criteria cannot be fully proven by static + axe-core. Always add a “Manual Review Queue” section to the report, including (at minimum):

- Keyboard-only navigation: order, traps, expected key behavior
- **WCAG 2.2 2.4.11 Focus Not Obscured (Minimum):** sticky UI does not cover focus
- Zoom/reflow: 320 CSS px width and 400% zoom behavior on key templates
- Pointer targets: **2.5.8 Target Size (Minimum)** spot-check small controls
- Drag-and-drop: **2.5.7 Dragging Movements** alternative exists
- Authentication (if applicable): **3.3.8 Accessible Authentication (Minimum)** support for password managers / copy/paste

If automated evidence is insufficient, categorize as “⚠️ Needs Manual Review” rather than claiming “Fixed”.

**Compare findings from both methods** to determine the best remediation strategy:

| Scenario | Confidence | Action |
|----------|------------|--------|
| **Both agree** (static + axe-core) | High | Auto-fix with confidence |
| **axe-core only** | High | Auto-fix (runtime-confirmed issue) |
| **Static only** | Medium | Report as potential issue; may need manual review |
| **axe-core incomplete** | Needs review | Flag for manual testing |

**Remediation priority:**
1. Issues confirmed by both methods (highest confidence)
2. axe-core violations (confirmed at runtime)
3. Static-only findings (report but use judgment on auto-fix)

**Progress output:**
```
🔍 Stage 1: Analyzing [target]
   ├─ Running static analysis (web-standards)...
   │   └─ Found X potential issues
   ├─ Starting local server...
   ├─ Running automated tests (axe-core via Playwright)...
   │   └─ Found Y violations, Z incomplete
   ├─ Comparing results...
   │   └─ X confirmed by both, Y axe-only, Z static-only
   └─ ✓ Remediation plan ready
```

**Proceed immediately to Stage 2.**

## Stage 2: Remediation

**Immediately after Stage 1 completes, proceed to Stage 2.** Do not stop. Do not ask permission.

For each issue, collaborate with specialized skills to determine the best fix, then apply it.

### Collaborative Remediation Planning

Before applying any fix, use axe-core recommendations as the primary guidance:

```
┌─────────────────────────────────────────────────────────────┐
│                 REMEDIATION WORKFLOW                         │
├─────────────────────────────────────────────────────────────┤
│  Issue from Stage 1                                         │
│         │                                                   │
│         ▼                                                   │
│  ┌─────────────────────────────────────────────────┐       │
│  │  axe-core recommendations (help, helpUrl,       │       │
│  │  failureSummary) ←── Primary Guidance           │       │
│  └──────────────────────┬──────────────────────────┘       │
│                         │                                   │
│                         ▼                                   │
│  ┌─────────────────────────────────────────────────┐       │
│  │  MCP Resources (if available):                  │       │
│  │  - WCAG guidelines & success criteria           │       │
│  │  - ARIA roles, states, properties               │       │
│  │  - Component accessibility patterns             │       │
│  └──────────────────────┬──────────────────────────┘       │
│                         │                                   │
│         ┌───────────────┴───────────────┐                  │
│         ▼                               ▼                  │
│  ┌─────────────┐                ┌─────────────┐            │
│  │a11y-personas│                │  Apply Fix  │            │
│  │    -mcp     │                │  to Code    │            │
│  └─────────────┘                └─────────────┘            │
└─────────────────────────────────────────────────────────────┘
```

> **Note:** For detailed WCAG criteria, ARIA specifications, and component patterns, use MCP servers if available. Skills focus on actions; MCP provides resources.

### Remediation Process

For each issue identified in Stage 1:

1. **Start with axe-core recommendations** — The axe-core results include remediation guidance:
   - `help`: Brief description of how to fix
   - `helpUrl`: Link to detailed Deque University documentation
   - `nodes[].failureSummary`: Specific fix instructions per element
   - Use this as the primary source for remediation planning

2. **Identify the component type** — What kind of element is this? (button, accordion, form field, etc.)

3. **Query MCP resources if available** — For additional context:
   - WCAG success criteria and techniques
   - ARIA roles, states, and properties
   - Component accessibility patterns and acceptance criteria

4. **Query `a11y-personas-mcp`** — Understand user impact:
   - `list-personas()` → Returns all 69 personas
   - `get-personas(["blindness-screen-reader-nvda"])` → Returns specific persona details
   - Which users are affected? How does it impact their experience?

5. **Apply the fix** — Based on axe-core guidance and MCP resources:
   - Prefer axe-core recommendations as the authoritative source
   - Use MCP resources to validate and enhance the fix
   - Consider user impact from personas when choosing between options

6. **Verify no visual impact** — Before applying:
   - Will this fix change how the page looks? If yes, flag for manual review
   - Does changing element types require CSS to preserve appearance?
   - Are we adding visible content? If yes, don't auto-fix

### Critical Constraint: No Visual Changes

**Clarification:** Some accessibility fixes *may* require visible changes to be truly effective (e.g., strengthening focus indication). If a fix materially affects visuals, do not silently change design; instead:
- apply only if it preserves the intended design system behavior, or
- flag as “Needs Manual Review” with a suggested, minimal design-safe approach.

**Fixes must be invisible to sighted users.** The page design MUST NOT change visually. Accessibility improvements happen "under the hood" through:
- Adding/fixing attributes (ARIA, alt text, labels)
- Changing element types while preserving appearance (div → button with CSS reset)
- Adding programmatic relationships (aria-labelledby, aria-describedby)
- Fixing JavaScript behavior (keyboard handlers, focus management)

**If a fix would change the visual design, flag it for manual review instead of applying it.**

### Critical Constraint: Remediate, Don't Add

**Only fix issues in code that already exists.** Do NOT:
- Add headings (`<h1>`, `<h2>`, etc.) that weren't in the original code
- Add landmark elements (`<header>`, `<nav>`, `<footer>`) that weren't there
- Add new structural elements that change the page design
- Add content or elements the designer/developer didn't include

**DO:**
- Fix accessibility issues in existing elements (add labels, fix ARIA, add alt text)
- Modify existing elements to be accessible (change `<div onclick>` to `<button>`)
- Add attributes to existing elements (`aria-expanded`, `aria-label`, etc.)
- Report structural issues (missing h1, missing landmarks) as "Needs Manual Review" for the developer to decide

### Fix Output Format

For each fixable issue, document:

```markdown
### Issue #N: [Brief description]

**axe-core Guidance:**
- Rule: [rule ID]
- Help: [help text]
- Deque Docs: [helpUrl]
- Fix suggestion: [failureSummary]

**WCAG:** [Success criterion if known]
**Personas affected:** [Query a11y-personas-mcp]

**Before:**
```html
[original code]
```

**After:**
```html
[fixed code]
```
```

For complex issues requiring manual review, document:
- Why auto-fix isn't appropriate
- Recommended approach for developer

**Progress output:**
```
🔧 Stage 2: Applying fixes
   ├─ Issue #1: [description]
   │   ├─ axe-core recommends: [fix suggestion]
   │   ├─ Checking personas... [X users affected]
   │   └─ ✓ Fix applied
   ├─ Issue #2: [description]
   │   └─ ...
   └─ ✓ Applied X fixes (Y need manual review)
```

**Proceed immediately to Stage 3.**

## Stage 3: Validation

**Immediately after Stage 2 completes, proceed to Stage 3.** Do not stop. Do not ask permission.

Verify that applied fixes resolve the issues.

### Validation Process

1. **Re-run tests** - Use `a11y-tester` skill to re-test the modified code (navigate and run axe-core again)
2. **Verify fixes resolved issues** - Compare before/after axe-core results
3. **Categorize results:**
   - ✅ Fixed - Issue no longer detected
   - ⚠️ Needs Manual Review - Cannot be automatically verified
   - ❌ Still Failing - Fix did not resolve the issue

**Progress output:**
```
✅ Stage 3: Validating fixes
   ├─ Re-running accessibility tests...
   ├─ Checking acceptance criteria...
   └─ ✓ X/Y issues resolved

📄 Report complete
```

**Output the final report. The workflow is now complete.**

## Report Template

Generate the final report using this structure:

```markdown
# Accessibility Remediation Report

**File:** [filename or URL]
**Date:** [current date]
**Status:** [emoji] X/Y confirmed issues resolved

## Summary

| Source | Issues | Fixed | Manual Review |
|--------|--------|-------|---------------|
| axe-core Violations | X | X | X |
| axe-core Incomplete | X | - | X |
| **Confirmed Total** | **X** | **X** | **X** |

## Resolved Issues (Confirmed by axe-core)

### Issue #N: [Brief description]
- **axe-core Rule:** [rule ID]
- **WCAG:** [SC number and name]
- **Severity:** [Critical/Serious/Moderate/Minor]
- **Impact:** [One sentence describing user impact]
- **Persona:** Affects users like [persona name] who [brief description]

**Before:**
```html
[original code]
```

**After:**
```html
[fixed code]
```

**Pattern:** [MagentaA11y link]

---

## Needs Manual Review (axe-core Incomplete)

### Issue #N: [Brief description]
- **axe-core Rule:** [rule ID]
- **Reason:** [Why automated testing couldn't determine pass/fail]
- **Guidance:** [Link to relevant documentation]

## Potential Issues (Static Analysis Only)

These issues were identified by static code analysis but NOT confirmed by automated testing. Review and address as needed:

### Issue #N: [Brief description]
- **Category:** [ARIA/Semantic HTML/Keyboard/etc.]
- **Recommendation:** [Suggested fix]
- **Note:** Not auto-fixed; requires developer decision

## Validation Summary

| Metric | Result |
|--------|--------|
| axe-core violations re-tested | X |
| Confirmed fixed | X |
| Still failing | X |
| Incomplete (manual review) | X |
| Static-only (not auto-fixed) | X |
```

## Handling Different Input Types

### HTML File
Run full static analysis. Runtime testing if file can be served.

### URL
Run both static (fetch source) and runtime (Playwright) analysis.

### Code Snippet
Run static analysis only. Provide fixes inline.

### Component/Framework Code
Identify framework (React, Vue, etc.) and adapt patterns accordingly.

## Quick Reference: Skill Delegation

| Task | Skill | Key Information |
|------|-------|-----------------|
| Static code analysis | `web-standards` | HTML/ARIA/semantic issues |
| Runtime testing | `a11y-tester` | axe-core violations (raw format) |
| Format issues | `a11y-issue-writer` | Standardized issue reports from violations |
| Generate fixes | `a11y-remediator` | Applies accessibility fixes |
| Verify fixes | `a11y-validator` | Confirms issues resolved |
| Base patterns | `a11y-base-web` | Foundational requirements |

## MCP Resources (External)

For detailed reference data, query these MCP servers:

| Resource | MCP Server | Repository | Example Tools |
|----------|------------|------------|---------------|
| WCAG guidelines | `wcag-mcp` | [github.com/joe-watkins/wcag-mcp](https://github.com/joe-watkins/wcag-mcp) | `get-criterion("4.1.2")`, `get-techniques-for-criterion("1.3.1")` |
| ARIA specs | `aria-mcp` | [github.com/joe-watkins/aria-mcp](https://github.com/joe-watkins/aria-mcp) | `get-role("button")`, `validate-role-attributes(...)` |
| Component patterns | `magentaa11y-mcp` | [github.com/joe-watkins/magentaa11y-mcp](https://github.com/joe-watkins/magentaa11y-mcp) | `get_web_component("button")`, `get_component_developer_notes("modal")` |
| User personas | `a11y-personas-mcp` | [github.com/joe-watkins/a11y-personas-mcp](https://github.com/joe-watkins/a11y-personas-mcp) | `list-personas()`, `get-personas(["blindness-screen-reader-nvda"])` |
| Issue templates | `accessibility-issues-template-mcp` | [github.com/joe-watkins/accessibility-issues-template-mcp](https://github.com/joe-watkins/accessibility-issues-template-mcp) | `format_axe_violation(...)`, `list_issue_templates()`, `get_issue_template(...)` |

> **Philosophy:** Skills are "doers" — they perform actions. MCP servers are "resources" — they provide reference data. Skills query MCP servers when they need specifications or patterns.

## Skills and MCP Integration

### Skills (Actions)

- **a11y-tester**: Runs axe-core tests and returns raw violations
- **a11y-issue-writer**: Formats violations into standardized issue reports (uses accessibility-issues-template-mcp)
- **a11y-remediator**: Applies fixes to code
- **a11y-validator**: Validates fixes
- **web-standards**: Performs static code analysis

**Workflow:** a11y-tester collects violations → a11y-issue-writer formats them (optional) → a11y-remediator fixes them → a11y-validator confirms

### MCP Servers (Resources)

MCP servers provide reference data that skills query during execution:

- **accessibility-issues-template-mcp**: Templates for formatting violations as issues (used by a11y-issue-writer)
- **wcag-mcp**: WCAG success criteria and techniques
- **aria-mcp**: ARIA roles, states, properties
- **magentaa11y-mcp**: Component accessibility patterns
- **a11y-personas-mcp**: User personas and impact information

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joe-watkins) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
