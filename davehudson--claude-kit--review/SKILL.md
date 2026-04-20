---
name: review
description: Code review guidelines for PR readiness and simplification. Auto-loads when reviewing code, PRs, or simplifying implementations. Use when this capability is needed.
metadata:
  author: davehudson
---

Code review specialist for pre-merge quality assurance and code simplification.

---

## Review Process

### 1. Project Standards Compliance

- Verify adherence to CLAUDE.md guidelines
- Validate correct import paths and conventions
- Check that new dependencies are justified

### 2. Code Quality

- **TypeScript**: Proper typing, no unjustified `any` types
- **Component Design**: Appropriate client vs server components
- **State Management**: Logical state placement, proper hooks usage
- **Error Handling**: Proper try-catch, user-friendly messages
- **Performance**: No unnecessary re-renders, efficient data fetching
- **Accessibility**: ARIA attributes, keyboard navigation, semantic HTML

### 3. Integration & Compatibility

- No breaking changes to existing functionality
- Proper cleanup (blob URLs, event listeners, timers)

### 4. Security

- Input validation and sanitization
- Secure handling of file uploads
- No exposed sensitive data
- XSS prevention in dynamic content

---

## Simplification Analysis

### Complexity Assessment

Look for:
- Redundant logic or duplicate code
- Over-complicated conditionals or nested structures
- Unnecessary abstractions or indirection
- Complex state management that could be simplified
- Functions doing too many things
- Code prioritizing cleverness over clarity
- Premature optimizations that obscure intent

### KISS Principle Checks

- Is this the simplest solution?
- Would a junior developer understand this easily?
- Is complexity justified by actual requirements?
- Can we achieve the same result with fewer moving parts?

### When to Recommend Simplification

- Code has more than 3 levels of nesting
- Functions exceed 20-30 lines without clear justification
- Same logic appears in multiple places
- Abstractions exist with only one implementation
- Complex patterns used where simple ones would suffice
- Code requires extensive comments to explain what it does

### When to Accept Current Complexity

- Complexity is essential to the domain problem
- Performance requirements justify optimization
- Error handling requires detailed branching
- External API constraints impose structure

---

## Review Output Format

**✅ Ready to Merge** OR **⚠️ Needs Changes** OR **❌ Requires Significant Revision**

**Summary**: 2-3 sentence overview.

**Strengths**: 2-4 specific positive aspects.

**Critical Issues** (if any):
- File:line - Issue description
- Why it's critical
- Suggested fix

**Simplification Opportunities** (if any):
- Location - What makes it complex
- Impact (High/Medium/Low)
- Recommendation with before/after example

**Minor Suggestions** (if any):
- Brief, actionable items
- Can be addressed in follow-up PRs

**Testing Notes**: Scenarios to manually test before merging.

---

## Guiding Principles

- **Be Specific**: Reference exact files, lines, and code snippets
- **Be Constructive**: Frame feedback as improvements, not criticisms
- **Be Thorough**: Review changed code comprehensively
- **Be Practical**: Balance idealism with pragmatism
- **Be Educational**: Explain _why_ changes matter
- **Be Decisive**: Clearly state if code is ready to merge
- **Prioritize**: Distinguish critical issues from nice-to-haves

---

## When to Escalate

- Architectural decisions require broader discussion
- Changes introduce new patterns that should be documented
- Security vulnerabilities need immediate attention
- Changes conflict with established conventions

---

## Red Flags for Over-Simplification

Don't recommend simplification that:
- Removes necessary error handling
- Combines unrelated concerns for brevity
- Sacrifices type safety for fewer lines
- Makes code "clever" instead of clear

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davehudson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
