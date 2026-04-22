---
name: code-reviewer
description: Conduct thorough code reviews of implemented features. Use this skill after implementation is complete to review code quality, security, performance, and requirements compliance. Creates structured review reports in context/ directory with actionable feedback and approval status. Use when this capability is needed.
metadata:
  author: swiftyjunnos
---

# Code Reviewer

## Overview

This skill performs comprehensive code reviews of implemented features, checking for requirements compliance, code quality, security issues, performance concerns, and testing readiness. It produces structured review reports that guide improvements and ensure code meets project standards.

## When to Use

Use this skill when:
- Feature implementation is complete
- Ready to review code before testing
- Need to verify requirements compliance
- Want to catch issues before they reach testing
- Preparing code for team review or merge

## Workflow

### Step 1: Prepare for Review

**Load Context:**
1. Read the requirements document from `context/` directory
2. Review implementation notes if available
3. Understand acceptance criteria and testing requirements

**Identify Changed Files:**
1. Use `git diff` or `git status` to see modified files
2. Review new files created for the feature
3. Check for related files that might be affected

### Step 2: Conduct Code Review

Review code systematically using the checklist in `references/review_checklist.md`.

#### Requirements Compliance Review

**Check Against Requirements:**
- Verify all features from requirements are implemented
- Confirm acceptance criteria are met
- Ensure no scope creep (only required features)
- Validate edge cases are handled

**Document Findings:**
- List each requirement and its compliance status
- Note any missing or incomplete features
- Highlight any deviations from requirements

#### Code Quality Review

**Readability:**
- Clear, descriptive naming
- Self-documenting code
- Appropriate comments for complex logic
- Consistent formatting
- No debug statements or commented code

**Structure:**
- Single-purpose functions
- Proper separation of concerns
- DRY principle followed
- Appropriate abstractions
- Reasonable file sizes

**TypeScript (if applicable):**
- Proper type annotations
- No unnecessary `any` types
- Well-defined interfaces/types
- No type errors or warnings

#### Best Practices Review

**React Patterns (if applicable):**
- Correct hook usage
- Proper component lifecycle
- Optimized re-renders
- Appropriate state management
- Effect cleanup

**Error Handling:**
- All error cases covered
- Meaningful error messages
- Proper logging (Logger, not console)
- No unhandled promises
- Try-catch where needed

**Performance:**
- No obvious bottlenecks
- Appropriate memoization
- Efficient algorithms
- Lazy loading where beneficial

**Security:**
- No hardcoded secrets
- Input validation
- XSS prevention
- SQL injection prevention
- Proper auth/authz

#### Testing Readiness Review

**Testability:**
- Code structure supports testing
- Dependencies can be mocked
- Pure functions where possible
- Side effects isolated
- Clear test boundaries

**Test Cases:**
- Identify unit test needs
- Identify integration test needs
- Note edge cases to test
- Document testing priorities

### Step 3: Document Review Findings

Create a review report using the template in `references/review_template.md`.

**Report Structure:**
1. **Summary:** Brief overview of changes and overall assessment
2. **Requirements Compliance:** Checklist with status
3. **Code Quality Assessment:** Strengths and issues categorized by severity
4. **Detailed Review by File:** File-by-file analysis with specific issues
5. **Security Review:** Security-specific findings
6. **Performance Review:** Performance considerations
7. **Testing Readiness:** Testability assessment and suggested test cases
8. **Recommendations:** Categorized action items (must fix, should fix, nice to have)
9. **Approval Status:** Decision and next steps

**Issue Severity Levels:**
- 🔴 **Critical:** Blocks approval, must fix immediately
  - Security vulnerabilities
  - Requirements not met
  - Breaking bugs
  - Major architectural issues

- 🟡 **Moderate:** Should fix before merge
  - Code quality issues
  - Missing error handling
  - Performance concerns
  - Incomplete features

- 🟢 **Minor:** Nice to have, can be addressed later
  - Naming improvements
  - Additional comments needed
  - Optimization opportunities
  - Style inconsistencies

### Step 4: Save Review Report

Save the review report to `context/review-{feature-name}.md`.

**Naming Convention:**
- Match the requirements document name
- Example: `requirements-user-auth.md` → `review-user-auth.md`
- Use timestamps if needed: `review-user-auth-20250104.md`

### Step 5: Communicate Findings

**Present Summary to User:**
1. Overall approval status
2. Number of issues by severity
3. Key strengths
4. Critical issues requiring immediate attention

**Decision:**
- ✅ **Approved:** Ready for testing
- ✅ **Approved with Comments:** Can proceed, minor issues noted
- ❌ **Changes Requested:** Must address issues before proceeding

**Provide Clear Next Steps:**
- If approved: Proceed to test generation
- If changes requested: List specific fixes needed
- If approved with comments: Note which issues can be deferred

### Step 6: Iterate if Needed

If changes are requested:
1. Wait for developer to address issues
2. Review the fixes
3. Update review report with new findings
4. Approve when ready

## Integration with Workflow

This skill is typically used as **Step 4** in the development workflow:

1. ✅ Analyze requirements (prompt-enhancer)
2. ✅ Document plan (requirements-documenter)
3. ✅ Implement features (implementation-guide)
4. ➡️ **Review code (this skill)**
5. Write tests (test-generator)
6. Final review (review-finalizer)

**Inputs:**
- Requirements document from `context/`
- Implemented code (changed/new files)
- Implementation notes (if available)

**Outputs:**
- Review report in `context/`
- Approval status and recommendations
- Suggested test cases for test-generator

**Used By:**
- Developer: Addresses review findings
- `test-generator`: Uses review insights for test creation
- `review-finalizer`: Confirms issues were resolved

## Review Focus Areas

### Always Check

1. **Requirements Completeness**
   - Every requirement implemented
   - No missing features
   - Edge cases covered

2. **Security**
   - No vulnerabilities
   - Proper input validation
   - Secrets management
   - Auth/authz correct

3. **Error Handling**
   - All errors caught
   - Meaningful messages
   - Proper logging
   - Graceful degradation

4. **Code Structure**
   - Clear organization
   - Proper abstractions
   - No duplication
   - Testable design

### Project-Specific Checks

Customize `references/review_checklist.md` for project-specific items:
- Specific coding standards
- Required patterns or abstractions
- Performance requirements
- Accessibility standards
- Team conventions

## Best Practices

**Do:**
- Be thorough but constructive
- Provide specific examples and locations
- Suggest concrete improvements
- Acknowledge good code and patterns
- Prioritize issues by severity
- Link to relevant documentation
- Consider the entire feature, not just individual files
- Think about maintainability

**Don't:**
- Nitpick style issues (use linters)
- Request changes outside requirements
- Be vague about issues
- Focus only on problems (note strengths too)
- Miss security or performance issues
- Skip the testing readiness assessment
- Forget to check against requirements document

## Example Usage

**Scenario:** Review user authentication implementation

**Step 1:** Load context
- Read `context/requirements-user-auth.md`
- Review implemented files: `authService.ts`, `useAuth.ts`, `LoginForm.tsx`

**Step 2:** Conduct review
- ✅ All requirements met
- 🟡 Missing error handling in one API call
- 🟢 Could add loading state to form
- ✅ Security looks good (tokens, validation)
- ✅ Code is testable

**Step 3:** Document findings
- Use `references/review_template.md`
- Fill in all sections
- Categorize issues by severity
- Provide specific line numbers and suggestions

**Step 4:** Save report
- Save as `context/review-user-auth.md`

**Step 5:** Communicate
- Status: **Approved with Comments**
- 1 moderate issue (missing error handling)
- 1 minor suggestion (loading state)
- Can proceed to testing, address moderate issue soon

**Result:** Clear, actionable review that guides improvements while allowing progress.

## Common Review Patterns

### First Implementation
- More thorough review
- Check architectural decisions
- Validate patterns for reusability
- Ensure it sets good precedent

### Iterative Feature
- Focus on consistency with existing code
- Check for proper use of abstractions
- Verify patterns match established code

### Bug Fix
- Confirm bug is actually fixed
- Check for similar issues elsewhere
- Ensure fix doesn't break other features
- Verify test added to prevent regression

### Refactoring
- Verify behavior unchanged
- Check for improved structure
- Ensure no new issues introduced
- Validate tests still pass

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/swiftyjunnos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
