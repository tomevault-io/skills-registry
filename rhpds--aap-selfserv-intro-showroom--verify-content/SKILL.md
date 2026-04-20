---
name: verify-content
description: Run comprehensive quality verification on workshop or demo content using Red Hat standards and validation agents. Use when this capability is needed.
metadata:
  author: rhpds
---

---
context: fork
model: sonnet
hooks:
  PreToolUse:
    - .claude/hooks/validate-paths.sh
---

# Content Verification Skill

Verify workshop or demo content against Red Hat quality standards, style guidelines, technical accuracy, and accessibility requirements.

## When to Use

**Use this skill when you want to**:
- Verify workshop content before publishing
- Check demo modules for quality and completeness
- Validate technical accuracy and Red Hat style compliance
- Review content for accessibility standards
- Get actionable feedback on content improvements

**Don't use this for**:
- Creating new content → use `/create-lab` or `/create-demo`
- Converting between formats → use `/blog-generate`

## Workflow

### Step 1: Identify Content Type

**Q: What type of content are you verifying?**

Options:
1. Workshop module (hands-on lab content)
2. Demo module (presenter-led demonstration)
3. Multiple files (specify pattern)

### Step 2: Locate Content

**For single file**:
- Provide file path (e.g., `content/modules/ROOT/pages/module-01-install-aap.adoc`)

**For multiple files**:
- Provide glob pattern (e.g., `content/modules/ROOT/pages/*.adoc`)
- Or directory path (e.g., `content/modules/ROOT/pages/`)

### Step 3: Run Verification Agents

I'll run comprehensive verification using these validation frameworks:

**For Workshop Content**:
1. `enhanced_verification_workshop.txt` - Overall quality assessment
2. `redhat_style_guide_validation.txt` - Red Hat style compliance
3. `verify_workshop_structure.txt` - Workshop structure validation
4. `verify_technical_accuracy_workshop.txt` - Technical accuracy
5. `verify_accessibility_compliance_workshop.txt` - Accessibility standards
6. `verify_content_quality.txt` - General content quality

**For Demo Content**:
1. `enhanced_verification_demo.txt` - Overall demo quality
2. `redhat_style_guide_validation.txt` - Red Hat style compliance
3. `verify_technical_accuracy_demo.txt` - Demo technical accuracy
4. `verify_accessibility_compliance_demo.txt` - Accessibility standards
5. `verify_content_quality.txt` - General content quality

### Step 4: Present Results

I'll provide:

**Summary Table**:
- Clean table with Issue, Priority, and Files columns
- No time estimates or fix duration
- Clear priority levels (Critical, High, Medium, Low)

**Strengths Section**:
- What your content does exceptionally well
- Positive highlights to reinforce good practices
- Recognition of quality work

**Detailed Breakdown** (optional, on request):
- Specific file locations and line numbers
- Before/after examples
- Implementation steps
- How to fix each issue

### Step 5: Offer Fixes (Optional)

After showing results, I can:
- Apply fixes automatically (with your approval)
- Provide code snippets for manual fixes
- Explain why each change improves quality

## Example Usage

### Example 1: Verify Single Workshop Module

```
User: /verify-content

Skill: What type of content are you verifying?
User: Workshop module

Skill: File path?
User: content/modules/ROOT/pages/module-01-install-aap.adoc

[Runs all workshop verification agents]

Skill:
📊 Summary Table of All Issues
┌──────────────────────────────────┬──────────┬───────────┐
│              Issue               │ Priority │   Files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing verification commands    │ Critical │ 1 file    │
├──────────────────────────────────┼──────────┼───────────┤
│ Incorrect product name "OCP"     │ Critical │ 1 file    │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing alt text for images      │ Critical │ 1 file    │
├──────────────────────────────────┼──────────┼───────────┤
│ Incomplete troubleshooting       │ High     │ 1 file    │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing learning outcomes        │ High     │ 1 file    │
├──────────────────────────────────┼──────────┼───────────┤
│ Commands lack expected output    │ Medium   │ 1 file    │
└──────────────────────────────────┴──────────┴───────────┘

---

✅ Strengths Worth Highlighting

Your module excels in these areas:

1. Clear Step-by-Step Instructions - Installation steps are well-structured and easy to follow
2. Proper AsciiDoc Formatting - Code blocks and syntax highlighting are correctly implemented
3. Strong Technical Content - Accurate AAP installation procedures for OpenShift

Would you like detailed breakdown of specific issues? [Yes/No]
```

### Example 2: Verify All Modules in Workshop

```
User: Verify all my workshop modules

Skill: Content location?
User: content/modules/ROOT/pages/

[Scans and finds 3 modules + index/overview]

Skill: Found 5 files:
- index.adoc
- 01-overview.adoc
- 02-details.adoc
- module-01-install-aap.adoc
- module-02-configure-aap.adoc

Verify all? [Yes/Select specific files]

User: Yes

[Runs verification on all files]

Skill:
📊 Summary Table of All Issues
┌──────────────────────────────────┬──────────┬───────────┐
│              Issue               │ Priority │   Files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Inconsistent heading styles      │ Critical │ All files │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing alt text for images      │ Critical │ 3 files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Title case headings              │ High     │ All files │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing Red Hat product names    │ High     │ 3 files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Incomplete verification commands │ Medium   │ 2 files   │
└──────────────────────────────────┴──────────┴───────────┘

---

✅ Strengths Worth Highlighting

Your workshop excels in these areas:

1. Excellent Business Context - Outstanding scenario in overview addressing real organizational challenges
2. Progressive Learning Flow - Well-structured progression from basic to advanced concepts
3. Strong Technical Depth - Comprehensive AAP configuration coverage across modules
4. Good Documentation Structure - Clear separation of overview, details, and hands-on modules

Would you like detailed breakdown for specific files? [Yes/No]
```

## Verification Standards

Every verification includes:

**Red Hat Style Guide**:
- ✓ Sentence case headlines
- ✓ Official Red Hat product names
- ✓ No prohibited terms (whitelist/blacklist, etc.)
- ✓ Proper hyphenation and formatting
- ✓ Serial comma usage

**Technical Accuracy**:
- ✓ Valid commands for current versions
- ✓ Correct syntax and options
- ✓ Working code examples
- ✓ Accurate technical terminology

**Workshop Quality** (for labs):
- ✓ Clear learning objectives
- ✓ Step-by-step instructions
- ✓ Verification commands with expected outputs
- ✓ Troubleshooting guidance
- ✓ Progressive skill building

**Demo Quality** (for demos):
- ✓ Know/Show structure
- ✓ Business value messaging
- ✓ Presenter guidance
- ✓ Visual cues for slides/diagrams
- ✓ Quantified metrics and ROI

**Accessibility**:
- ✓ Alt text for all images
- ✓ Proper heading hierarchy
- ✓ Clear, inclusive language
- ✓ Keyboard-accessible instructions

**Content Quality**:
- ✓ Complete prerequisites
- ✓ Consistent formatting
- ✓ Proper AsciiDoc syntax
- ✓ References and citations
- ✓ Professional tone

## Output Format

Results are presented in clear, actionable format:

```markdown
📊 Summary Table of All Issues
┌──────────────────────────────────┬──────────┬───────────┐
│              Issue               │ Priority │   Files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Duplicate References sections    │ Critical │ 3 files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing descriptive alt text     │ Critical │ 3 files   │
├──────────────────────────────────┼──────────┼───────────┤
│ Title case headings              │ High     │ All files │
├──────────────────────────────────┼──────────┼───────────┤
│ Missing blank lines before lists │ High     │ 2 files   │
├──────────────────────────────────┼──────────┼───────────┤
│ "Powerful" usage                 │ High     │ 4 files   │
└──────────────────────────────────┴──────────┴───────────┘

---

✅ Strengths Worth Highlighting

Your workshop excels in these areas:

1. Exceptional RBAC Implementation Guidance - Module 01 provides comprehensive step-by-step RBAC configuration that's production-ready
2. Strong Business Context - Outstanding business scenario addressing real organizational challenges
3. Excellent Verification Sections - Checkpoints with ✅ expected results and troubleshooting are exemplary
4. Perfect External Link Formatting - ALL external links correctly use ^ caret (opens in new tab)
5. Clear Persona-Based Learning - User persona approach effectively demonstrates RBAC in action

---

## Detailed Issue Breakdown (on request)

### 1. Missing Verification Commands
**File**: module-01-install-aap.adoc:145
**Impact**: Learners can't verify success, leading to confusion
**Priority**: Critical

**Current**:
```asciidoc
. Deploy the AutomationController:
```

**Fixed**:
```asciidoc
. Deploy the AutomationController:
+
[source,bash]
----
oc get automationcontroller -n ansible-automation-platform
----
+
Expected output:
----
NAME                  STATUS   AGE
platform-controller   Running  5m
----
```

**How to fix**:
1. Add verification command after deployment step
2. Include expected output
3. Add success indicator
```

## Priority Levels

Issues are categorized by priority:

- **Critical**: Must fix before publishing - impacts functionality, accessibility, or brand compliance
- **High**: Should fix soon - affects quality and user experience significantly
- **Medium**: Recommended fixes - improves overall quality
- **Low**: Nice to have - polish and optimization

## Integration with Other Skills

**After `/create-lab`**:
- Run verification on generated module
- Apply fixes before committing
- Ensure quality standards met

**After `/create-demo`**:
- Verify Know/Show structure
- Check business messaging
- Validate presenter guidance

**Before publishing**:
- Final verification of all content
- Batch check entire workshop
- Generate quality report

## Tips for Best Results

**Be specific about content type**:
- Workshop modules use different standards than demos
- Infrastructure files (nav.adoc, README.adoc) have different requirements

**Review before auto-fix**:
- Understand why changes are recommended
- Some fixes may need manual adjustment
- Technical accuracy requires domain knowledge

**Run verification regularly**:
- After creating new modules
- Before submitting PRs
- After major content updates

## Quality Standards

Every verification run checks:
- ✓ Red Hat brand compliance
- ✓ Technical accuracy for current versions
- ✓ Accessibility (WCAG 2.1 AA)
- ✓ Learning effectiveness
- ✓ Professional formatting
- ✓ Complete documentation
- ✓ Consistent style

## Files Used

**Verification prompts** (in `.claude/prompts/`):
- `enhanced_verification_workshop.txt`
- `enhanced_verification_demo.txt`
- `redhat_style_guide_validation.txt`
- `verify_workshop_structure.txt`
- `verify_technical_accuracy_workshop.txt`
- `verify_technical_accuracy_demo.txt`
- `verify_accessibility_compliance_workshop.txt`
- `verify_accessibility_compliance_demo.txt`
- `verify_content_quality.txt`

**Reference examples**:
- `content/modules/ROOT/pages/workshop/example/`
- `content/modules/ROOT/pages/demo/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rhpds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
